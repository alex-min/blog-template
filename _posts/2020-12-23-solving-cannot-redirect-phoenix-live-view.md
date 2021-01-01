---
title: Solving 'cannot redirect socket on update/2' with Phoenix Live view components
description: 
author: alex-min
image: /images/phoenix.png
html_class: "article-with-error-blocks"
tags:
  - elixir
  - phoenix
---

Maybe you've encountered this ```cannot redirect socket on update/2``` error while using a live_component and a put_redirect on Phoenix live view:

```
[error] GenServer #PID<0.1068.0> terminating
** (RuntimeError) cannot redirect socket on update/2
    (phoenix_live_view 0.15.0) lib/phoenix_live_view/utils.ex:413: Phoenix.LiveView.Utils.maybe_call_update!/3
    (phoenix_live_view 0.15.0) lib/phoenix_live_view/diff.ex:324: Phoenix.LiveView.Diff.component_to_rendered/4
    (phoenix_live_view 0.15.0) lib/phoenix_live_view/diff.ex:366: Phoenix.LiveView.Diff.traverse/6
    (phoenix_live_view 0.15.0) lib/phoenix_live_view/diff.ex:427: anonymous fn/4 in Phoenix.LiveView.Diff.traverse_dynamic/6
    (elixir 1.11.2) lib/enum.ex:2181: Enum."-reduce/3-lists^foldl/2-0-"/3
    (phoenix_live_view 0.15.0) lib/phoenix_live_view/diff.ex:339: Phoenix.LiveView.Diff.traverse/6
    (phoenix_live_view 0.15.0) lib/phoenix_live_view/diff.ex:580: Phoenix.LiveView.Diff.render_component/9
    (phoenix_live_view 0.15.0) lib/phoenix_live_view/diff.ex:194: Phoenix.LiveView.Diff.write_component/5
    (phoenix_live_view 0.15.0) lib/phoenix_live_view/channel.ex:463: Phoenix.LiveView.Channel.component_handle_event/6
    (stdlib 3.13.2) gen_server.erl:680: :gen_server.try_dispatch/4
    (stdlib 3.13.2) gen_server.erl:756: :gen_server.handle_msg/6
    (stdlib 3.13.2) proc_lib.erl:226: :proc_lib.init_p_do_apply/3
```

This is actually easy to solve, your component just needs to have an **id**:

```elixir
<%= live_component @socket, MyApp.MyComponent, id: "something" %>
```

The reason being that stateful components needs to have an id for the lifecycle function to do its job properly. Now you are done!

