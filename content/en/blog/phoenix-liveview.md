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

``` elixir
def broadcast({:ok, message}, event) do 
  Phoenix.PubSub.broadcast(App.PubSub, "topic_name", message)
end`
```

``` elixir
message = {:message_name, actual_message}
```


## LIVE COMPONENTS


### @impl true

Tells LiveView to implement the callback

```elixir
@impl true 
def render(assigns) do
  ~L"""
  whatever
  """
```


### Stateful by adding `id` and pointing to the Component with `@myself`

Add `id` to the live_component to make it 'stateful'

```elixir
<%= live_component @socket, MessageComponent, id: 1 %>
```

Add `@myself` to the stateful form to make the Component handle the event

```elixir
<form phx-submit="send-message" phx-target="<%= @myself %>" %>
```
 



### Card Component with Form

```elixir
defmodule CardComponent do

  def render(assigns) do
    ~H"\""
    <form phx-submit="..." phx-target={@myself}>
      <input name="title"><%= @card.title %></input>
      ...
    </form>
    "\""
  end
```


### Listing of cards

```elixir
<%= for card <- @cards do %>
  <%= live_component CardComponent, card: card, id: card.id, board_id: @id %>
<% end %>
```

Form submission triggers `CardComponent.handle_event/3` which must update the card. 


## LiveView as the Source: self()

The component and the view run in the same process. So, sending an internal message from the LiveComponent to the parent LiveView is done by sending a message to `self()`:


```elixir
send self(), {:message_name, %{content_of_message}}
```



```elixir
defmodule CardComponent do
  ...
  def handle_event("update_title", %{"title" => title}, socket) do
    send self(), {:updated_card, %{socket.assigns.card | title: title}}
    {:noreply, socket}
```

LiveView then receives this event using `c:Phoenix.LiveView.handle_info/2`:

```elixir
defmodule BoardView do
  ...
  def handle_info({:updated_card, card}, socket) do
    # update the list of cards in the socket
    {:noreply, updated_socket}
```

The LiveView will send the updated card to the component.

Alternatively, the could be updated via broadcast by using `Phoenix.PubSub` to all users subscribed to it.
 
```elixir
defmodule CardComponent do
  ...
  def handle_event("update_title", %{"title" => title}, socket) do
    message = {:updated_card, %{socket.assigns.card | title: title}}
    Phoenix.PubSub.broadcast(MyApp.PubSub, board_topic(socket), message)
    {:noreply, socket}
  end

  defp board_topic(socket) do
    "board:" <> socket.assigns.board_id
```


## LiveComponent as the Source

In this case, LiveView must only fetch the card ids, then render each component only by passing an ID:

```elixir
<%= for card_id <- @card_ids do %>
  <%= live_component CardComponent, id: card_id, board_id: @id %>
<% end %>
```

Each CardComponent will load its own card making expensive N queries, where N is the number of cards. So we use the `c:preload/1` callback to make it efficient.

Once the card components are started, they can each manage their own card, without concern for the parent LiveView.

Components do not have a `c:Phoenix.LiveView.handle_info/2` callback. Therefore, if you want to track distributed changes on a card, you must have LiveView receive those events and redirect them to the appropriate card. 

For example, if card updates are sent to the "board:ID" topic, and that the board LiveView is subscribed to the  said topic, one could do:

```elixir
def handle_info({:updated_card, card}, socket) do
  send_update CardComponent, id: card.id, board_id: socket.assigns.id
  {:noreply, socket}
```

With `Phoenix.LiveView.send_update/3`, the `CardComponent` given by `id` will be invoked, triggering both preload and update callbacks, which will load the most up to date data from the database.


## LiveComponent blocks

When [`live_component/3`](`Phoenix.LiveView.Helpers.live_component/3`) is invoked, it is also possible to pass a `do/end` block:

```elixir
<%= live_component GridComponent, entries: @entries do %>
  <% entry -> %>New entry: <%= entry %>
<% end %>
```

The `do/end` will be available in an assign named `@inner_block`.

You can render its contents by calling `render_block` with the assign itself and a keyword list of assigns to inject into the rendered
content. For example, the grid component above could be implemented as:

```elixir
defmodule GridComponent do
  use Phoenix.LiveComponent

  def render(assigns) do
    ~H"\""
    <div class="grid">
      <%= for entry <- @entries do %>
        <div class="column">
          <%= render_block(@inner_block, entry) %>
        </div>
      <% end %>
    </div>
    "\""
  end
end
```

Where the `entry` variable was injected into the `do/end` block.

Note the `@inner_block` assign is also passed to `c:update/2`
along all other assigns. So if you have a custom `update/2`
implementation, make sure to assign it to the socket like so:

```elixir
def update(%{inner_block: inner_block}, socket) do
  {:ok, assign(socket, inner_block: inner_block)}
end
```