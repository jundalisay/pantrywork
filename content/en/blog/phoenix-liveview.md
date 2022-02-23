+++
title = "Phoenix Live View"
banner = "/img/phx.jpg"
description = "We list down essential concepts in Phoenix Live View"
tags = ['Phoenix']
date = 2022-01-22
+++

Live View is a Genserver that turns Elixir or Phoenix into a Websocket Server. This eliminates the need for Javascript frameworks like React or Vue. Live Components replace React Components. Though you can still use React if you want to make life difficult for yourself. 

`GET` in REST is `mount` or `patch` for websockets

Change a state by sending an event:

``` elixir
phx-value-id="<%= struct.id %>"
```
``` elixir
%{"input-data" => elixir-variable-repersenting-a-string}
```

### LIVE ROUTES

Live Routes are `PATCH` because it updates or patches the LiveView process with the new data and sends a new 'diff' to the DOM

``` elixir
<%= live_patch "Text", 
  to: Routes.live_path(@socket, __CURRENT-MODULE__, id: struct.id) %>
```

__CURRENT-MODULE__ is a shortcut for the Module Name


#### handle_params

- handles actions that affect Routes via live_patch sends an event as data-phx-link-state
- always invoked after mount
- handles dynamic states, just as mount handles static states

``` elixir
handle_params(params-of-url, url, socket) do 
  {:noreply, socket}
```

``` elixir
def handle_params(%{"id" => id}, params-of-url, url, socket) do 
  {:noreply, socket}
end
def handle_params(%{_, _url, socket) do # handles empty params 
  {:noreply, socket}
end

def render(data) do
  always-needed-assigns = %{key: data}
  ~L"""
    <%= @key %>
  """
end
```