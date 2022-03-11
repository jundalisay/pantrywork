+++
title = "Elixir Data Types"
subtitle = ""
banner = "/img/phx.jpg"
description = "We list Elixir Data Types"
tags = ['Elixir']
date = 2022-03-10
+++

### Convert string to integer or float

``` elixir
# returns tuple e.g. {123.45, ""}
Integer.parse(n)
Float.parse(n)

# returns integer
String.to_integer(n)
String.to_float(n)


Decimal.new(n) |> Decimal.to_integer
Decimal.new(n) |> Decimal.to_float
```


Example
``` elixir
"1.0 1 3 10 100" |> String.split |> Enum.map(fn n -> Float.parse(n) |> elem(0) end)
[1.0, 1.0, 3.0, 10.0, 100.0]
```


### Convert float to string

``` elixir
n |> to_string([decimals: 2, compact: true])
```



### Convert float to charlist

``` elixir
n |> to_charlist()
```



<!-- Integer.parse("01") # {1, ""}
Integer.parse("01.2") # {1, ".2"}
Integer.parse("0-1") # {0, "-1"}
Integer.parse("-01") # {-1, ""}
Integer.parse("x-01") # :error
Integer.parse("0-1x") # {0, "-1x"}
Similarly
String.to_integer/1
has the following results:
String.to_integer("01") # 1
String.to_integer("01.2") # ** (ArgumentError) argument error
:erlang.binary_to_integer("01.2")
String.to_integer("0-1") # ** (ArgumentError) argument error
:erlang.binary_to_integer("01.2")
String.to_integer("-01") # -1
String.to_integer("x-01") # ** (ArgumentError) argument error
:erlang.binary_to_integer("01.2")
String.to_integer("0-1x") # ** (ArgumentError) argument error
:erlang.binary_to_integer("01.2")
Instead, validate the string first.
re = Regex.compile!("^[+-]?[0-9]*\.?[0-9]*$")
Regex.match?(re, "01") # true
Regex.match?(re, "01.2") # true
Regex.match?(re, "0-1") # false
Regex.match?(re, "-01") # true
Regex.match?(re, "x-01") # false
Regex.match?(re, "0-1x") # false -->