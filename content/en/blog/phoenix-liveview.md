+++
title = "Phoenix Live View Basics"
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

## SOCKET

- a struct that has an 'assigns' map 
- the assigns map has the LiveView state info
- 'socket assigns' therefore means the map-data

``` elixir
{:ok, socket}
{:no_reply, socket}
{:error, socket}
```


### mount(map_of_query_router_params, session_data, socket_struct)

- creates a LiveView process from the router
- assigns the state of the LiveView process to the socket struct
- must return a `{:ok, socket}` tuple
- can be revealed by `IO.inspect(socket)`

``` elixir
def mount(map_of_query_router_params, session_data, socket_struct) do
  {:ok, assign(socket_struct, :key, value)}
end
```


### render(assigns_map)

- needs to output content as a 'sigil'

``` elixir
~L"""
<%= @key_in_the_assigns %>
"""
```





## BINDINGS IN TEMPLATE

### phx-click

- binds a click event to an element then sends it to the LiveView process through the socket 





### LIVE ROUTES

Live Routes are `PATCH` because it updates or patches the LiveView process with the new data and sends a new 'diff' to the DOM

``` elixir
<%= live_patch "Text", 
  to: Routes.live_path(@socket, __CURRENTMODULE__, id: struct.id) %>
```

`__CURRENTMODULE__` is a shortcut for the Module Name




## HANDLERS

- These render the changes in the data (or "state") inside the socket-struct-assign-map. 


#### handle_params

- handles URL parameters that affect Routes via live_patch
- why not call it as handle_url??
- sends an event as data-phx-link-state
- always invoked after `mount`
- handles dynamic states, just as mount handles static states

``` elixir
handle_params(parameters_of_url, url, socket) do 
  # manipulate the socket data here to automatically output and update the view
  {:noreply, socket}
```


``` elixir
# has parameters
def handle_params(%{"id" => id}, parameters_of_url, url, socket) do 
  # manipulate the socket data via the id state
  {:noreply, socket}
end

# empty parameters
def handle_params(%{_, _url, socket) do
  # if no url parameter is entered then just show the socket
  {:noreply, socket}
end

# rendering whatever
def render(data) do
  always_needed_assigns = %{key: data}
  ~L"""
    <%= @key %>
  """
end
```




### handle_event

- handles external messages (events) from the template bindings such as `phx-click`
- why not call it `handle_external???`

``` elixir
def handle_event("name_of_external_message_or_event_in_html", metadata_about_the_event, socket_struct) do
  assign(socket, :key, value)
  {:no_reply, socket}
end 
```



#### handle_info

- handles internal messages (info) 

``` elixir
def handle_info(name_of_internal_message, socket_struct) do
  assign(socket, :key, value)
  {:no_reply, socket}
end 
```




## Subscribe - Broadcast

### Subscribe 

``` elixir
  def subscribe do
    Phoenix.PubSub.subscribe(App.PubSub, "topic_name")
  end
```

### Broadcast

