+++
title = "Phoenix Live View Components"
banner = "/img/phx.jpg"
description = "We list down essential concepts in Phoenix Live View Components"
tags = ['Phoenix']
date = 2022-04-22
+++

Components run inside the LiveView process, but may have their own state and event handling. They are either stateless or stateful.

**Setting** 

```elixir
Phoenix.LiveComponent
```

**Calling** 

```elixir
Phoenix.LiveView.Helpers.live_component/3` in a parent LiveView
```

### Simplest Stateless Component: `render/1`

```elixir
defmodule HeroComponent do
use MyAppWeb, :live_component

def render(assigns) do
  ~L\"""
  <div><%= @content %></div>
  \"""
end
end
```


### Passing Data to Component from Parent LiveView

```elixir
<%= live_component HeroComponent, content: @content %>
```


## Stateless components

`live_component/3` calls three things:

```elixir
mount(socket) -> update(assigns, socket) -> render(assigns)
```

- `mount/1` sets the initial state
- `update/2` sets all of the assigns given to `live_component/3`. If `c:update/2` is not defined, all assigns are simply merged into the socket
- `render/1` renders all assigns


A stateless component is always mounted, updated, and rendered whenever the parent template changes.


## Stateful Components via`:id`

Components become stateful by passing an `:id` assign.

```elixir
<%= live_component HeroComponent, id: :hero, content: @content %>
```

They are identified by the component module and their ID. Therefore, two different component modules with the same ID are different components. This means we can often tie the component ID to some application based ID:

```elixir
<%= live_component UserComponent, id: @user.id, user: @user %>
```

The given `:id` is not necessarily used as the DOM ID. If you want to set a DOM ID, it is your responsibility to set it when rendering:

```elixir
defmodule UserComponent do
use Phoenix.LiveComponent

def render(assigns) do
  ~L\"""
  <div id="user-<%= @id %>" class="user"><%= @user.name %></div>
  \"""
end
end
```

Stateful components should only a single root element in the HTML template. 

In stateful components, `c:mount/1` is called only once when it is first rendered. For each rendering, the optional   `c:preload/1` and `c:update/2` callbacks are called before `c:render/1`.

So on first render, the following callbacks will be invoked:

```elixir
preload(list_of_assigns) -> mount(socket) -> update(assigns, socket) -> render(assigns)
```

On subsequent renders, these callbacks will be invoked:

```elixir
preload(list_of_assigns) -> update(assigns, socket) -> render(assigns)
```

### Component Events

For a client event to reach a component, the tag must have `phx-target`. Use `@myself` to send it to the component assign.<!--  which is an *internal unique reference* to the
component instance: -->

```elixir
<a href="#" phx-click="say_hello" phx-target="<%= @myself %>">
Say hello!
</a>
```

`@myself` is not set for stateless components. To target another component, pass an ID or a class selector to any element inside the targeted component. Only the diff of the component is sent to the client, making them extremely efficient.

```elixir
<a href="#" phx-click="say_hello" phx-target="#user-13">
Say hello!
</a>
```


Any valid query selector for `phx-target` is supported, provided that the matched nodes are children of a LiveView or LiveComponent, for example to send the `close` event to multiple components:

```elixir
<a href="#" phx-click="close" phx-target="#modal, #sidebar">
Dismiss
</a>
```

<!-- ### Preloading and update

Every time a stateful component is rendered, both `preload/1` and `update/2` are called. 

```elixir
<%= live_component UserComponent, id: user_id %>
```

A possible implementation would be to load the user on the `c:update/2`
callback:

    def update(assigns, socket) do
      user = Repo.get! User, assigns.id
      {:ok, assign(socket, :user, user)}
    end

However, the issue with said approach is that, if you are rendering
multiple user components in the same page, you have a N+1 query problem.
The `c:preload/1` callback helps address this problem as it is invoked
with a list of assigns for all components of the same type. For example,
instead of implementing `c:update/2` as above, one could implement:

    def preload(list_of_assigns) do
      list_of_ids = Enum.map(list_of_assigns, & &1.id)

      users =
        from(u in User, where: u.id in ^list_of_ids, select: {u.id, u})
        |> Repo.all()
        |> Map.new()

      Enum.map(list_of_assigns, fn assigns ->
        Map.put(assigns, :user, users[assigns.id])
      end)
    end

Now only a single query to the database will be made. In fact, the
preloading algorithm is a breadth-first tree traversal, which means
that even for nested components, the amount of queries are kept to
a minimum.

Finally, note that `c:preload/1` must return an updated `list_of_assigns`,
keeping the assigns in the same order as they were given.
-->

## Managing state

The parent LiveView and its LiveComponent should not work on 2 different copies of the state. Only one is the truth. 

In this scenario, each LiveView Card has a form to update the card title directly:

```elixir
defmodule CardComponent do
use Phoenix.LiveComponent

def render(assigns) do
  ~L\"""
  <form phx-submit="..." phx-target="<%= @myself %>">
    <input name="title"><%= @card.title %></input>
    ...
  </form>
  \"""
end

...
end
```

### LiveView as the source of truth

The board LiveView will fetch all the cards in a board, calling `live_component/3` for each card, passing the card struct as argument to `CardComponent`:

```elixir
<%= for card <- @cards do %>
<%= live_component CardComponent, card: card, id: card.id, board_id: @id %>
<% end %>
```

When the user submits the form, `CardComponent.handle_event/3` the Component sends an internal message to its parent Liveview via `self()`:

```elixir
defmodule CardComponent do
...
def handle_event("update_title", %{"title" => title}, socket) do
  send self(), {:updated_card, %{socket.assigns.card | title: title}}
  {:noreply, socket}
end
end
```

The LiveView receives this event using `Phoenix.LiveView.handle_info/2`:

```elixir
defmodule BoardView do
...
def handle_info({:updated_card, card}, socket) do
  # update the list of cards in the socket
  {:noreply, updated_socket}
end
end
```

The parent LiveView will be re-rendered, sending the updated card to the Component, or by broadcasting via `Phoenix.PubSub`. This needs the LiveView to subscribe to the `board:<ID>` topic

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
end
end
```


### LiveComponent as the source of truth

The board LiveView no longer fetches the card structs from the database. Instead, it only fetches the card ids, then render each component by passing an ID:

```elixir
<%= for card_id <- @card_ids do %>
  <%= live_component CardComponent, id: card_id, board_id: @id %>
<% end %>
```

Each CardComponent will load its own card. You should use `preload/1` 

To broadcast changes on a card, the parent LiveView must receive those events and redirect them to the appropriate card. 

<!--   For example, assuming card updates are sent
to the "board:ID" topic, and that the board LiveView is subscribed to
said topic, one could do: -->

```elixir
def handle_info({:updated_card, card}, socket) do
send_update CardComponent, id: card.id, board_id: socket.assigns.id
{:noreply, socket}
end
```

`Phoenix.LiveView.send_update/3` invokes the `CardComponent` given by `id`. This triggers preload and update callbacks.


## LiveComponent blocks

`do/end` block is possible with `live_component/3` 

```elixir
<%= live_component GridComponent, entries: @entries do %>
New entry: <%= @entry %>
<% end %>
```

The `do/end` will be available in an assign named `@inner_block`. You can render its contents by calling `render_block` with the assign itself and a keyword list of assigns to inject into the rendered   content. For example, the grid component above could be implemented as:

```elixir
defmodule GridComponent do
use Phoenix.LiveComponent

def render(assigns) do
  ~L\"""
  <div class="grid">
    <%= for entry <- @entries do %>
      <div class="column">
        <%= render_block(@inner_block, entry: entry) %>
      </div>
    <% end %>
  </div>
  \"""
end
end
```

Where the `:entry` assign was injected into the `do/end` block.

The `@inner_block` assign is also passed to `c:update/2` along all other assigns. So if you have a custom `update/2` implementation, make sure to assign it to the socket like so:

```elixir
def update(%{inner_block: inner_block}, socket) do
{:ok, assign(socket, inner_block: inner_block)}
end
```

The above approach is the preferred one when passing blocks to `do/end`. However, if you are outside of a .leex template and you want to invoke a component passing a `do/end` block, you will have to explicitly handle the assigns by giving it a `->` clause:

```elixir
live_component GridComponent, entries: @entries do
  new_assigns -> "New entry: " <> new_assigns[:entry]
end
```

## Live patches and live redirects

A template rendered inside a component can use:
- `Phoenix.LiveView.Helpers.live_patch/2` : this is always handled by the parent`LiveView`, as components do not provide `handle_params`
- `Phoenix.LiveView.Helpers.live_redirect/2` 



## Cost of stateful components

### Keep only the assigns necessary in each component. 

Avoid passing all of LiveView's assigns when rendering a component:

```elixir
    <%= live_component MyComponent, assigns %>
```

Instead pass only the keys that you need:

```elixir
<%= live_component MyComponent, user: @user, org: @org %>
```

The view and the component will share the same copies of the `@user` and `@org` assigns.

### Avoid using stateful components to provide abstract DOM components

A good LiveComponent encapsulates application concerns and not DOM functionality. If your page shows products, you can encapsulate those products in a component. This component may have many buttons and events inside. 

Do not write a component that is simply encapsulating generic DOM components: 

```elixir
defmodule MyButton do
  use Phoenix.LiveComponent

  def render(assigns) do
    ~L\"""
    <button class="css-framework-class" phx-click="click">
      <%= @text %>
    </button>
    \"""
  end

  def handle_event("click", _, socket) do
    _ = socket.assigns.on_click.()
    {:noreply, socket}
  end
end
```

Instead, create a function:

```elixir
def my_button(text, click) do
  assigns = %{text: text, click: click}

  ~L\"""
  <button class="css-framework-class" phx-click="<%= @click %>">
      <%= @text %>
  </button>
  \"""
end
```

## Limitations

1. Components require at least one HTML tag

Components must only contain HTML tags at their root. At least one HTML tag must be present. It is not possible to have components that render
only text or text mixed with tags at the root.

2. Components must always be change tracked

If you render a component inside `form_for` the component ends up enclosed by the form markup, where LiveView
cannot track it.

```elixir
<%= form_for @changeset, "#", fn f -> %>
  <%= live_component SomeComponent, f: f %>
<% end %>
```

This causes an error: 

```elixir
    ** (ArgumentError) cannot convert component SomeComponent to HTML.
    A component must always be returned directly as part of a LiveView template
```

Solve this without anonymous functions:

```elixir
<%= f = form_for @changeset, "#" %>
  <%= live_component SomeComponent, f: f %>
</form>
```

This issue can also happen with other helpers, such as `content_tag`:

```elixir
<%= content_tag :div do %>
  <%= live_component SomeComponent, f: f %>
<% end %>
```

So do not use `content_tag`. Instead, use LiveEEx to build the markup.

<!--   ### SVG support

Since SVG allows `<svg>` tags to be nested, you can wrap the component content into an `<svg>` tag. This will ensure that it is correctly interpreted by the browser.

```elixir
defmodule CID do

  defstruct [:cid]

  defimpl Phoenix.HTML.Safe do
    def to_iodata(%{cid: cid}), do: Integer.to_string(cid)
  end

  defimpl String.Chars do
    def to_string(%{cid: cid}), do: Integer.to_string(cid)
  end
end

alias Phoenix.LiveView.Socket

defmacro __using__(_) do
  quote do
    import Phoenix.LiveView
    import Phoenix.LiveView.Helpers
    @behaviour Phoenix.LiveComponent

    require Phoenix.LiveView.Renderer
    @before_compile Phoenix.LiveView.Renderer

    @doc false
    def __live__, do: %{kind: :component, module: __MODULE__}
  end
end

@callback mount(socket :: Socket.t()) ::
            {:ok, Socket.t()} | {:ok, Socket.t(), keyword()}

@callback preload(list_of_assigns :: [Socket.assigns()]) ::
            list_of_assigns :: [Socket.assigns()]

@callback update(assigns :: Socket.assigns(), socket :: Socket.t()) ::
            {:ok, Socket.t()}

@callback render(assigns :: Socket.assigns()) :: Phoenix.LiveView.Rendered.t()

@callback handle_event(
            event :: binary,
            unsigned_params :: Phoenix.LiveView.unsigned_params(),
            socket :: Socket.t()
          ) ::
            {:noreply, Socket.t()} | {:reply, map, Socket.t()}

@optional_callbacks mount: 1, preload: 1, update: 2, handle_event: 3
end
``` -->