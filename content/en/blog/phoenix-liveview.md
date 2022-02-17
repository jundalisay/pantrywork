+++
title = "Phoenix Live View"
banner = "/img/phx.jpg"
description = "We list down essential concepts in Phoenix Live View"
tags = ['Phoenix']
date = 2022-01-22
+++

change a state by sending an event

phx-value-id="<%= struct.id %>"

%{"input-data" => elixir-variable-repersenting-a-string}


### LIVE ROUTES

<%= live_patch "Text", to: Routes.live_path(@socket, __CURRENT-MODULE__, id: struct.id) %>
- patch because it updates the LiveView process with the new data and sends a new diff to the DOM

__CURRENT-MODULE__ 

handles actions that affect Routes via live_patch sends an event as data-phx-link-state

    handle_params(params-of-url, url, socket) do 
      {:noreply, socket}

- always invoked after mount
- handles dynamic states, just as mount handles static states


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
