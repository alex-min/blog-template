---
title: "Building a multiplayer card game with Phoenix Live View (part 1)"
description:  Create a multiplayer game!
author: alex-min
image: /images/thinking-elixir-cover.png
tags:
  - elixir
  - phoenix
---

## Welcome on this new series on how to build a multiplayer card game!

I wanted as a side-project to create a web equivalent of everyone's favourite drinking game, cards against humanity!

Here are the rules summarized:

 - There's two types of cards, answers and questions. You can play one or multiple answers cards depending on the question
 - One player does not play and will act as the judge to award points to the winner, for this version I've chosen to cycle trough each player to designate the judge.
 - Cards should act like in a physical game, cards played should not be seen again and should not be distributed twice.

I will only detail the interesting parts of the project, if you want to check out the source code, you can check the full source on [Github](http://github.com).

## Building the lobby

In order to build this game, we must have some equivalent to a "game lobby". 
Here are some of the features I would like to have:

 - Players can join there at any time.
 - A fixed URL should exist so that you can share it with your friends.
 - Players can set a nickname

First let's build a small GameServer, all the live views will communicate to this server.

```elixir
# lib/carte_limite_web/game_server.ex
defmodule CarteLimiteWeb.GameServer do
  use GenServer, restart: :transient

  def subscribe(id) do
    Phoenix.PubSub.subscribe(CarteLimite.PubSub, id)
  end

  def broadcast(id, value) do
    Phoenix.PubSub.broadcast!(CarteLimite.PubSub, id, value)
  end

  def start_link([id]) do
    GenServer.start_link(
      __MODULE__, %{
        #... all the game state 
      }, name: process_name(id))
  end

  defp process_name(id) do
    {:via, Registry, {GameRegistry, id}}
  end
end

# lib/carte_limite/application.ex
defmodule CarteLimite.Application do
  #...
  def start(_type, _args) do
    children = [
      CarteLimite.Repo,
      #...
      CarteLimiteWeb.GameSupervisor,
      {Registry, keys: :unique, name: GameRegistry}
    ]
  end
end
```

Each game will have a unique id associated with it and this public id will be used in the url to access the game. There's no authentication planned here, if you have the public url, you end up on the lobby.


```elixir
# lib/carte_limite_web/controllers/new_game_controller.ex
defmodule CarteLimiteWeb.NewGameController do
  use CarteLimiteWeb, :controller

  def index(socket, _) do
    id = CarteLimiteWeb.GameSupervisor.spawn_game()
    socket |> redirect(to: "/game/#{id}")
  end
end

# lib/carte_limite_web/game_supervisor.ex
defmodule CarteLimiteWeb.GameSupervisor do
  use Supervisor

  #...
  
  def spawn_game() do
    id = random_string(20)

    Supervisor.start_child(__MODULE__, %{
      id: id,
      start: {GameServer, :start_link, [[id]]}
    })

    id
  end
end
```


