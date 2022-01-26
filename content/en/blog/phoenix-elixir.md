+++
title = "Elixir and Phoenix Concepts"
banner = "/img/phx.jpg"
description = "We list down essential concepts in Elixir and Phoenix"
tags = ['Phoenix']
date = 2022-01-22
+++

# Elixir

## Guard Clauses

    `def function-name(input) when condition do`
    `end`


## Anonymous Functions

`fn(input1, input2, input3..) (disposables)`
  square = fn -> x * x end 

    anon square.(4)
    named square(4)

    &($1 * %1) 
    variables &capture


### Defining

    function_name = fn(a,b,c,d) -> (a,b,c,d)

    function_name = &(&1 + &2 + &3 + &4)

    function_name = &(Module.method/4)


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




### Calling

function_name.(1,2,3,4)


## Plugs: Module Plug

### Defining

    def init(opts) do
      opts
    end 

    def call()



    Controller 

    alias Appname.Plugname

    plug 



## Function Plug

### Defining

    function_name = fn(a,b,c,d) -> (a,b,c,d)
    function_name = &(&1 + &2 + &3 + &4)
    function_name = &(Module.method/4)

### Calling

    @plug_name


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




alias Module #just identifies which Module to look into
import Module #gets all functions of Module
@constant = value




Agents (state management)

