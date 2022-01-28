+++
title = "Elixir and Phoenix Concepts"
banner = "/img/phx.jpg"
description = "We list down essential concepts in Elixir and Phoenix"
tags = ['Phoenix']
date = 2022-01-22
+++

# Elixir

## Pattern Matching Rules

`=` is a 'match operator' not an equals sign
- accepts left and right 'operands', with left being dominant
- if left is a letter/s, the right is bound to that letter/s-variable `x = 1`

`x = 'a'`
`'b' = x` # error because left must be a letter/s or number such as 1 + 1 = 2
`b = b` # error because right side needs non-variable


- if right is a letter/s, the right is matched to the value of the left `x = y`
- entering new match operators `x = 2` will re-bind the operands
- pin operator `^` prevents re-binding so it prevents the operand does not change

### Integer Separator

`1123_456_789` --> `123456789`


### Booleans

Strict: and, or, not
Non-strict: &&, ||, !

### Comparison
> 
<
>=
<=

#### Non-Strict Comparison
!=
==

#### Strict Comparison
!==
===


### String literals 

`\` escape character which indicates a special string with certain abilities
- `\n` new line eg: `"Donald\nTrump"`

`#{}` interpolation inserts expression within the string
- `"Donald Trump #{'J' == 'r' == '.'}"



### Atom literal 

used as labels or tags

    :text
    :"<>" 
    true  


## Recursion 


## Iteration 


## Conditionals


<br>

## Pipe Operator

passes output of earlier function or pipe to the new one


## Guard Clauses

Filters functions

    def function_name(input) when condition1 do
    end

    def function_name(input) when condition2 do
    end


## Functions

enclosed in Modules


`def function_name(argument_name) do`

def function_name(argument_name\\ "default-value-if-no-argument-name-is-given") do


### Anonymous Functions

**Setting**

`function_name = fn(argument1, argument2, argument3..) -> argument1 + argument2 + argument3.. end`

eg:
- `square = fn(x) -> x * x end`
- `function_name = fn(a,b,c,d) -> (a,b,c,d)
- function_name = &(&1 + &2 + &3 + &4)
- function_name = &(ModuleName.another_function/4)


**Calling** 

- anonymous function: `function_name.(4)`
- named function: `function_name(4)`


#### Capture Operator %

if `&1`, then shortcut for `fn(x..) -> x..`
- &1 is first argument 
- &2 is second argument..

eg: `function_name = &(&1 *&2)`

if function, then `&(Module.function_name/1)`

eg: `var_too_lazy_to_type_module = &(LongNamedModule.long_named_function/100)`
 
<!-- &($1 * %1) 

variables &capture
 -->


### Erlang Functions

`:timer`



## Module

has the functions

    defmodule ModuleName do
      def function-name(argument1, argument2,..) do 
        ModuleNameSomewhereElse.function-name()


`alias Module` #just identifies which Module to look into

`import Module` #gets all functions of Module

`@constant = value` #compiled 


## Plug has a 'Conn' Struct

takes and returns a conn struct between modules
conn has the data for the request


### Plug Module

getting in Phoenix
= @

plug = dsl webserver
cowboy = webserver


defmodule App.Modulename do
  import Plug.Conn

  def init(opts) do
    opts
  end

  def call(conn, _opts) do 
  end

end


<br>

### Calling

function_name.(1,2,3,4)


<br>

## Plugs: Module Plug

### Defining

    def init(opts) do
      opts
    end 

    def call()



    Controller 

    alias Appname.Plugname

    plug 



## Function Plug: Adds a Conn to a Function Module

**Defining**

    defmodule App.ModuleName do
      import Plug.Conn

      def get_total(conn, options) do 
        assign(conn, :get_total, 123)
      end 

    end 

<!--     function_name = fn(a,b,c,d) -> (a,b,c,d)
    function_name = &(&1 + &2 + &3 + &4)
    function_name = &(Module.method/4) -->

**Calling**

defmodule App.TotalController do
  import App.ModuleName
  plug :get_total
  ..
end 

in layout:
<%= @conn.assigns[:get_total] %>





## Map

### Defining

    map = %{:atom => "string", "string" => "string"}
    mapshortcut = %{atom: "string", atom2: "string"}

### Calling

    map.atom
    map["string"]
    map[:atom]

Examples: 

    map = %{ph -> manila, us -> wash}

    pipe operator puts the result in the last series

    name(1,2,3,4)

    function_name = &(Calculator.subtract/2)


## Ecto Queries

### Marco method









### Schema attributes

@primary_key
    @primary_key {:uuid, :binary_id, autogenerate: true}

@schema_prefix
@foreign_key_type
@timestamp_opts


## Phoenix

### Changeset

def change_tablename(%Tablename{} = tablename, attrs \\ %{}), do: Tablename.changeset(tablename, attrs)  # creates a changeset named-struct from the changeset settings based on change


Agents (state management)


