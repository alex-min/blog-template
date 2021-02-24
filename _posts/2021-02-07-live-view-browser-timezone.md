---
title: Managing browser timezones to display dates with Phoenix Live View
description:  Dates, timezones and locales
author: alex-min
image: /images/phoenix-scroll.png
tags:
  - elixir
  - phoenix
---

<a class="github-preview" href="https://github.com/alex-min/phoenix-timezone-demo" target="_blank">
  <img src="/images/github-logo-white.png" alt="Github" width="100">
  <span>Checkout the demo project on GitHub</span>
</a>

I'm currently building a web version of my personal finance application, for that, I've chosen to use [Phoenix Live View](https://github.com/phoenixframework/phoenix_live_view), it's the perfect tech for my use case.

In this finance web application, you do have a lot of dates to display such as when you actually spent the money. Users (including myself) can also travel so the dates needs to be displayed according to the current browser timezone.

There's a simple way to get the timezone in the browser:

```javascript
new Date().getTimezoneOffset(); // returns the timezone offset in minutes
```

So here are the main things we need to do:
  - Send the browser timezone to the Live View
  - Store the timezone in the session. This is needed for the server side rendering since we also want to display the right date even on the server side before the live view mounts. If we don't do this, the user is going to see the wrong date first and then the view will be re-rendered properly once the socket connects, this isn't a great UX and will look janky.
  - Retreive the timezone either from the Live socket parameters or the session depending if we're on the server side rendering. 

### Sending the timezone

Luckily for us, there's a simple way to send parameters on the initialisation of the Live Socket:

```javascript
let liveSocket = new LiveSocket("/live", Socket, {
    params: {
        timezone: - (new Date().getTimezoneOffset() / 60)
    }
})
```

That's as simple as that! This enables us to receive the timezone when the live view is connected, [```Phoenix.LiveView.get_connect_params/1```](https://hexdocs.pm/phoenix_live_view/Phoenix.LiveView.html#get_connect_params/1) can then be used to retreive that data if the socket is connected.

The first part being completed, we need to create a small controller which stores the timezone into the session:

```elixir
defmodule MavioWeb.SessionSetTimezoneController do
  use MavioWeb, :controller

  def set(conn, %{"timezone" => timezone}) when is_number(timezone) do
    conn |> put_session(:timezone, timezone) |> json(%{})
  end
end
```

On the first page load, we need to call this controller to store the timezone in the session, for that a small bit of Javascript is needed:

```javascript
export default function sendTimezoneToServer() {
    const timezone = - (new Date().getTimezoneOffset() / 60);
    let csrfToken = document.querySelector("meta[name='csrf-token']").getAttribute("content")


    if (typeof window.localStorage != 'undefined') {
        try {
            // if we sent the timezone already or the timezone changed since last time we sent
            if (!localStorage["timezone"] || localStorage["timezone"].toString() != timezone.toString()) {
                var xhr = new XMLHttpRequest();
                xhr.open("POST", '/api/session/set-timezone', true);
                xhr.setRequestHeader("Content-Type", "application/json");
                xhr.setRequestHeader("x-csrf-token", csrfToken);
                xhr.onreadystatechange = function () {
                    if (this.readyState === XMLHttpRequest.DONE && this.status === 200) {
                        localStorage['timezone'] = timezone.toString();
                    }
                };
                xhr.send(`{"timezone": ${timezone}}`);
            }
        } catch (e) { }
    }
}
```

I'm using XMLHttpRequest since I also need to support older browsers but you might want to use [```window.fetch```](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) or [Axios](https://www.npmjs.com/package/axios) which have much nicer apis.

### On the Live View

On the Live View we just retreive the timezone, either from the session or from the socket parameters depending of what we have available:

```elixir
  def get_timezone(socket, session) do
    if Phoenix.LiveView.connected?(socket) do
      case Phoenix.LiveView.get_connect_params(socket) do
        %{"timezone" => timezone} -> timezone
        _ -> session["timezone"] || 0
      end
    else
      session["timezone"] || 0
    end
  end
```

At this point you are probably thinking why I'm bothering with the session if we can send this data every time the socket is initialized? There's a very simple reason for that, Live views are rendered server side before the socket is actually connected, if we did not do that session step, the dates would simply be rendered with the wrong timezone the first time and then "corrected" once the socket is actually connected, that would be a poor user experience.


I've then created a simple Live view helper to display the date according to the timezone and the local:

```elixir
  def to_datestring(date, _locale, _timezone) when date == nil or date == "" do
    ""
  end

  def to_datestring(date, locale, timezone) when is_binary(date) do
    {:ok, parsed_date, _} = DateTime.from_iso8601(date)

    {:ok, str} =
      Mavio.Cldr.DateTime.to_string(parsed_date |> Timex.shift(hours: timezone), locale: locale)

    str
  end

  @spec to_datestring(DateTime.t() | String.t(), String.t(), integer()) :: String.t()
  def to_datestring(date, locale, timezone) do
    {:ok, str} =
      Mavio.Cldr.DateTime.to_string(date |> Timex.shift(hours: timezone), locale: locale)

    str
  end
```

This helper supports using strings or actual DateTime objects, I'm using the excellent [Timex](https://hexdocs.pm/timex/Timex.html) library to shift the timezone offset and [Cldr](https://github.com/elixir-cldr/cldr) for internationalization.

And here we go, our objective is completed! Dates can now be displayed in multiple languages and multiple timezones!