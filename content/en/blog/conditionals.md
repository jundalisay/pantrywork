+++
title = "Elixir Data Types"
subtitle = ""
banner = "/img/phx.jpg"
description = "We list Elixir Data Types"
tags = ['Elixir']
date = 2022-03-10
+++


m = %{a: 1, c: 3}

a =
  with {:ok, number} <- Map.fetch(m, :a),
    true <- is_even(number) do
      IO.puts "#{number} divided by 2 is #{div(number, 2)}"
      :even
  else
    :error ->
      IO.puts("We don't have this item in map")
      :error
    _ ->
      IO.puts("It is odd")
      :odd
  end


with {:ok, variable} <- Module.action(changeset),
     {:ok, token, full_claims} <- Guardian.encode_and_sign(user, :token, claims) do
  important_stuff(token, full_claims)
end
