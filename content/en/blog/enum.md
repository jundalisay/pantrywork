+++
title = "Elixir Enums"
subtitle = ""
banner = "/img/phx.jpg"
description = "Here are a few Enum methods"
tags = ['Phoenix']
date = 2022-03-15
+++

Unlike object-oriented programming languages, Elixir uses structs a lot. This requres a way to manipulate structs which is done by `Enum` and functions. 


## all?

applies a function to all elements

```elixir
iex> Enum.all?(["foo", "bar", "hello"], fn(s) -> String.length(s) == 3 end) # false because hello fails
```

## any?

applies a function to all elements and returns true if at least one elements is true

```elixir
iex> Enum.any?(["foo", "bar", "hello"], fn(s) -> String.length(s) == 5 end) # true
```

## chunk_every

Breaks up a list into smaller groups by size

```elixir
iex> Enum.chunk_every([1, 2, 3, 4, 5, 6], 2)
[[1, 2], [3, 4], [5, 6]]
```

## chunk_by

Breaks up a list into smaller groups according to function

```elixir
iex> Enum.chunk_by(["one", "two", "three", "four", "five", "six"], fn(x) -> String.length(x) end)
[["one", "two"], ["three"], ["four", "five"], ["six"]]
```


## find

```elixir
Enum.find(array, default_result, fn x -> method_using_x end)
```

Finds the element in the list that fulfills the function, otherwise returns a default result

```elixir
Enum.find([2, 3, 4], fn x -> rem(x, 2) == 1 end) #3

Enum.find([2, 4, 6], fn x -> rem(x, 2) == 1 end) # nil

Enum.find([2, 4, 6], 0, fn x -> rem(x, 2) == 1 end) # 0 as the default result
```

## map_every

<!-- Sometimes chunking out a collection isn’t enough for exactly what we may need. If this is the case, map_every/3 can be very useful to hit every nth items, always hitting the first one: -->

Applies the function every n items

```elixir
iex> Enum.map_every([1, 2, 3, 4, 5, 6, 7, 8], 3, fn x -> x + 1000 end)
[1001, 2, 3, 1004, 5, 6, 1007, 8]
```

## each

Get each element, returning the atom :ok

```elixir
iex> Enum.each(["one", "two", "three"], fn(s) -> IO.puts(s) end)
one
two
three
:ok
```

## map

Applies a function to each element and creates a new list with the results <!-- collection look to the map/2 function: -->

```elixir
iex> Enum.map([0, 1, 2, 3], fn(x) -> x - 1 end)
[-1, 0, 1, 2]
```


## min/1

Finds the minimal value in the list:

```elixir
iex> Enum.min([5, 3, 0, -1])
-1
```

### min/2 

In case the enumerable is empty, it lets us specify a function to produce the minimum value.

```elixir
iex> Enum.min([], fn -> :foo end)
:foo
```

## max

returns the maximal value in the collection:

```elixir
iex> Enum.max([5, 3, 0, -1])
5
```

max/2 is to max/1 what min/2 is to min/1:

```elixir
iex> Enum.max([], fn -> :bar end)
:bar
```

## filter

The filter/2 function enables us to filter the collection to include only those elements that evaluate to true using the provided function.

```elixir
iex> Enum.filter([1, 2, 3, 4], fn(x) -> rem(x, 2) == 0 end)
[2, 4]
```

## reduce

With reduce/3 we can distill our collection down into a single value. To do this we supply an optional accumulator (10 in this example) to be passed into our function; if no accumulator is provided the first element in the enumerable is used:

```elixir
iex> Enum.reduce([1, 2, 3], 10, fn(x, acc) -> x + acc end)
16
```

```elixir
iex> Enum.reduce([1, 2, 3], fn(x, acc) -> x + acc end)
6
```

```elixir
iex> Enum.reduce(["a","b","c"], "1", fn(x,acc)-> x <> acc end)
"cba1"
```

## sort/1

Sorts lists

```elixir
iex> Enum.sort([:foo, "bar", Enum, -1, 4])
[-1, 4, Enum, :foo, "bar"]
```

## sort/2

Uses two sorting functions.

```elixir
iex> Enum.sort([%{:val => 4}, %{:val => 1}], fn(x, y) -> x[:val] > y[:val] end)
[%{val: 4}, %{val: 1}]
```
sort/2 allows :asc or :desc

```elixir
Enum.sort([2, 3, 1], :desc)
[3, 2, 1]
```

## uniq/1

Remove duplicates:

```elixir
iex> Enum.uniq([1, 2, 3, 2, 1, 1, 1, 1, 1])
[1, 2, 3]
```


## uniq_by/2

allows a function to do the uniqueness comparison.

```elixir
iex> Enum.uniq_by([%{x: 1, y: 1}, %{x: 2, y: 1}, %{x: 3, y: 3}], fn coord -> coord.y end)
[%{x: 1, y: 1}, %{x: 3, y: 3}]
```

## Enum using the Capture operator (&)

& represent anonymous functions


```elixir
iex> Enum.map([1,2,3], fn number -> number + 3 end)
[4, 5, 6]
```


Now we implement the capture operator (&); capturing each iterable of the list of numbers ([1,2,3]) and assign each iterable to the variable &1 as it is passed through the mapping function.

```elixir
iex> Enum.map([1,2,3], &(&1 + 3))
[4, 5, 6]
```

This can be further refactored to assign the prior anonymous function featuring the Capture operator to a variable and called from the Enum.map/2 function.

```elixir
iex> plus_three = &(&1 + 3)
iex> Enum.map([1,2,3], plus_three)
[4, 5, 6]
```

## & and named function

<!-- First we create a named function and call it within the anonymous function defined in Enum.map/2. -->

```elixir
  def add_tatlo(number) do
    number + 3
  end
```

```elixir
iex>  Enum.map([1,2,3], fn number -> Module.add_tatlo(number) end)
[4, 5, 6]
```

```elixir
iex> Enum.map([1,2,3], &Module.add_tatlo(&1))
[4, 5, 6]
```

We can call the named function without explicitly capturing the variable.

```elixir
iex> Enum.map([1,2,3], &Module.add_tatlo/1)
[4, 5, 6]
```
