+++
title = "Elixir and Phoenix Concepts"
banner = "/img/phx.jpg"
description = "We list down essential concepts in Elixir and Phoenix"
tags = ['Phoenix']
date = 2022-01-22
+++

# Elixir

`hd` head

``` elixir
hd(array-name) #returns the first element or head
```

`_` 

``` elixir
_whatever #tells Elixir that `whatever` can be null
```

``` elixir
tuple {}
map %{}
```

## Pipe Operator

``` elixir
|>
```

passes output of earlier function into the next function due to the functional or flowing nature of Elixir. This requires the functional programmer to have more intelligence than an object-oriented programmer as to remember the long chain of cause and effect

puts the result in the last series
<!-- 
mix = gem
hex = rvm
mix phoenix.new --version
mix local.hex = gem install hex  -->


## Pattern Matching Rules

`=` is a 'match operator' not an equals sign. This requires more intelligence from the programmer as to decide whether two sides match or not, instead of being equal to just one side like in object-oriented or procedural programming.
- accepts left and right 'operands', with left being dominant
- if left is a letter/s, the right is bound to that letter/s-variable `x = 1`

``` elixir
x = 'a'
'b' = x # error because left must be a letter/s or number such as 1 + 1 = 2
b = b # error because right side needs non-variable
```

- if right is a letter/s, the right is matched to the value of the left `x = y`
- entering new match operators `x = 2` will re-bind the operands
- pin operator `^` prevents re-binding so it prevents the operand does not change

### Integer Separator

``` elixir
1123_456_789 --> 123456789
```

### Booleans

Strict:
``` elixir
and
or
not
```

Non-strict: 
``` elixir
&& 
|| 
!
```


### Comparison

``` elixir
>
<
>=
<=
```

#### Non-Strict Comparison

``` elixir
!=
==
```

#### Strict Comparison

``` elixir
!==
===
```

### String literals 

`\` escape character indicates a special string with certain abilities
- `\n` new line eg: 

``` elixir
"Donald\nTrump"
```

`#{}` interpolation inserts expression within the string

``` elixir
"Donald Trump #{'J' == 'r' == '.'}"
```


### Atom literal 

used as labels or tags

``` elixir
:text
:"<>" 
true  
```

## Maps

### Structs: Specialized Maps

#### Defining

``` elixir
defmodule ModuleName do
  defstruct [:key1, :key2]
end
```

#### Calling

``` elixir
%ModuleName{key1: value, key2: value}

module = %ModuleName{key1: value, key2: value}
module.key1
```

> data type must match! (atoms vs strings)



## Recursion 


## Iteration 


## Conditional Macros for the Lazy

`->` if-then

``` elixir
case condition do
    true -> a
    false -> b
end 
```

``` elixir
case mega_function(input) do
    {:error, error_message} -> {:error, error_message}
    {:ok, mega_function_output} -> case mini_function(input) do  
        {:error, error_message} -> {:error, error_message}
        {:ok, input} -> %{key: mini_function_output}
    end          
end 
```



`with <-` if-then, if-then, if-then

``` elixir
case mega_function(input) do
    with {:ok, mini_function_output} <- mini_function(input)} do
        {:ok, %{key: mini_function_output} 
        # mega_function_ouput has mini_function_output inside of it
    end          
end 
```


## Guard Clauses

Filters functions

``` elixir
def function_name(input) when condition1 do
end

def function_name(input) when condition2 do
end
```


## Functions

enclosed in Modules

``` elixir
def function_name(argument_name) do

def function_name(argument_name\\ "default-value-if-no-argument-name-is-given") do
```

### Anonymous Functions

**Setting**

``` elixir
function_name = fn(argument1, argument2, argument3..) -> argument1 + argument2 + argument3.. end
```

eg:

``` elixir
square = fn(x) -> x * x end
function_name = fn(a,b,c,d) -> (a,b,c,d)
function_name = &(&1 + &2 + &3 + &4)
function_name = &(ModuleName.another_function/4)
```

**Calling** 

``` elixir
function_name.(4) #anonymous function
function_name(4) #named function
```

#### Capture Operator: Shortcut for those lazy to write code

`&1` is the shortcut for *the arguments* in `fn(x..) -> x..`

``` elixir
&1 #represents or captures the first argument 
&2 #represents or captures the second argument
```

eg: 

``` elixir
function_name = &(&1 * &2)
```

``` elixir
&(Module.function_name/1) #shortcut for the whole Module
```

``` elixir
var_too_lazy_to_type_module = &(LongNamedModule.long_named_function/100)
```

<!-- &($1 * %1) 

variables &capture
 -->


### Erlang Functions

``` elixir
:timer
```
``` elixir
IO.puts "whatever"
```


<br>

## Module

container for the functions

``` elixir
defmodule ModuleName1 do
  def function_name(argument1, argument2,..) do 
    ModuleName2.function_name()
```


### Alias and Import: References to modules

``` elixir
alias Module # just identifies which Module to look into eventually

import Module # actually gets all the functions of Module

@constant = value # assigns a value as a constant at compile-time 
```


## Plug: Manipulates data in conn structs

It's a Module that has a 'Conn' Struct. It takes and returns that conn struct between modules. 

It allows state management <!-- (in Flutter) --> when combined with Agents and Genservers (like Live View). The state is held by the struct and then is passed between Modules. The ability to pass data turns Plugs into Elixir webservers

It has two types:
- Module Plug
- Function Plug


### Assigns 

``` elixir
assign(key: "#{data.attrib}")
```

<!-- plug = dsl webserver
cowboy = webserver -->

### Function Plug

This is a simple function that receives a conn struct, manipulates it, and outputs the modified conn struct 

#### Defining `non_piped` and `piped` Function Plugs

``` elixir
defmodule App.ModuleName do
  import Plug.Conn

  def non_pipe(conn, options) do 
    assign(conn, :non_pipe, 123)
  end

  def piped(conn, _options) do
    conn
    |> put_resp_content_type("text/plain") 
    |> send_resp(200, "From a Piped Function Plug that outputted this data inside this conn struct")
  end

end 
```

#### Calling in Elixir

``` elixir
plug :non_piped
```

#### Calling in Phoenix View

```
<%= @conn.assigns[:non_piped] %>
<%= @non_piped %>
```


### Module Plug

This is a large function, as a Module, that needs to be initialized with additional data such as state (i.e. initial state). This makes it more complicated than a function plug

#### Defining

``` elixir
defmodule App.Modulename do
  import Plug.Conn

  def init(options) do
    options
  end

  def call(conn, _options) do 
  end

end
```

#### Calling in Elixir

``` elixir
alias ModulePlugName
plug ModulePlugName, [option: "Blah"] when action is [:index]
```

#### Calling

``` elixir
Controller 

alias Appname.Plugname

plug

function_name.(1,2,3,4)
```

<br>


## Function Plug: Adds a Conn to a Module Function


<!--     function_name = fn(a,b,c,d) -> (a,b,c,d)
    function_name = &(&1 + &2 + &3 + &4)
    function_name = &(Module.method/4) -->

#### Calling

``` elixir
defmodule App.TotalController do
  import App.ModuleName
  plug :get_total
  ..
end 
```

<!-- in layout:
<%= @conn.assigns[:get_total] %> -->


## Map

### Defining

``` elixir
map = %{:atom => "string", "string" => "string"}
mapshortcut = %{atom: "string", atom2: "string"}
```

### Calling

``` elixir
map.atom
map["string"]
map[:atom]
```

Examples: 

``` elixir
map = %{ph -> manila, us -> wash}
```

<!-- name(1,2,3,4)

function_name = &(Calculator.subtract/2) -->

# OTP Processes and State Management

This allows functions to hold data independent of the function which is really held through the Genserver. <!-- In other web languages, this ability is done through an in-memory database such as Redis which holds the state for the webserver. --> This makes the OTP a real app. 


## Processes

- runs the actual functions and modules in memory 
- has PID


## Tasks: Start-Stops a Function or Module on a Process

- Runs a single process such as polling
- 1 task : 1 process
- has no state

``` elixir
Task.start(function_name)
```

## Agents: Adds Lightweight State management on a Process 

Only gets and updates single states. This makes it faster. 


## Genservers: Adds Heavy Duty State management on a Process

Gets, manipulates, and updates states by allowing "messages".




# Ecto

Handles external data such as databases and JSON APIs

- Repo
- Query
- Schema
- Changset

## Repo: communicates with external data source or database

Has common methods:
- get(): gets the record
- insert(): creates the record in that database
- update()
- delete()
- transaction()
...

Phoenix uses Repo through:
- `config.exs` as config `:appname, Appname.Repo,` ...
- `repo.ex` in `/lib/appname/repo.ex`

#### Common methods

``` elixir
Repo.count()
Repo.update_all("tablename", set: [updated_at: Ecto.DateTime.utc])
```

### Schema

Reusable data-model for moving data around. Schemas are a useful shortcut used in querying so you don't have to specify all the attributes 

<!-- attrib instead of property -->

### Schema Associations 

sets relationships between tables 

- has_many
- has_one
- belongs_to
- many_to_many

#### Virtual Attributes

Disposable attributes that are not saved to the database. For example, an office address that is used to get a latitude and longitude to store in the database, without storing the office address

#### Embedded Schema

A schema that doesn't use a datbase


### Changeset

Allows manipulation of the data to match Schema so that it can be passed to the Repo

- Cast: filters the parameters with the attributes to be accepted into the changeset
- Validate: validates those filtered attributes. This includes constraints which are set on the database level

#### Defining 

``` elixir
def changeset(schemaname, attrs) do
  schemaname
  |> cast(attrs, [:key1, :key2,..])
  |> validate_required([:key1])
  |> validate_inclusion(:name, ["John", "Jack"])
  |> validate_exclusion(:name, ["Jub Jub"])
  |> validate_length(:country, is: 2)
  |> validate_length(:password, min: 8, max: 32)
end
```


#### Calling

``` elixir
attrib_name = get_field(changeset, :attrib_name)
```


### Schemaless Changeset


#### Defining

``` elixir    
model = %{key1: :string, key2: string} 
attributes = %{key1: "value", key2: "value"}    
changeset = {%{}, model}
  |> cast(attributes, Map.keys(model))
  |> validate_required([:key1])
end
```

## Ecto Queries (see [other article](/blog/ecto-queries))

<!-- @primary_key
    @primary_key {:uuid, :binary_id, autogenerate: true}

@schema_prefix
@foreign_key_type
@timestamp_opts
 -->


## Phoenix

### Phoenix Ecto

allows changeset shortcuts into forms 


<!-- Creates a changeset named-struct from the changeset settings based on change:

`def change_tablename(%Tablename{} = tablename, attrs \\ %{}), do: Tablename.changeset(tablename, attrs)` -->


<!-- Functional programming is continuous where each function is connected to each other with the and the main advantage is that the state management.  -->

<!-- Object-oriented programming is not continuous. Functions are split into objects. This makes it easy for the mind.  -->