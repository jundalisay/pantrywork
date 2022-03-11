+++
title = "Ecto Queries"
subtitle = ""
banner = "/img/phx.jpg"
description = "Here are a ecto queries"
tags = ['Phoenix']
date = 2022-02-15
+++

# Repo

Repo has a limited number of queries. 

## Return a single record, and raise an error if more than one record is returned

``` elixir
Repo.one
```

## Check for existence

``` elixir
Repo.exists?
```


## Get by something

### Get a record by id


Pipe

``` elixir
User |> Repo.get(1) 
User |> Repo.get!(1)
```

No Pipe

``` elixir
Repo.get(User, 1)
Repo.get!(User, 1)
```


### Get first or last record

Pipe 

``` elixir
User |> first |> Repo.one
User |> last |> Repo.one
```

No Pipe 

``` elixir
last(User)
first(User)
```


### Get a single record by a single attribute


Pipe 

``` elixir
User |> Repo.get_by(first_name: "John")
User |> Repo.get_by!(first_name: "John")
```

``` elixir
Repo.get_by(User, first_name: "John")
Repo.get_by!(User, first_name: "John") # error if more than 1 record
```


# 

---

# Ecto Queries

This is needed for more complex queries that involve `where` `select` `order_by` etc. 
- Keyword queries follow the pattern `from` `where` `select`
- Macro queries or Query Expresssions use the Schema name and pipes

These require: 

``` elixir
import Ecto.Query
```

Otherwise you'll get the common error:

``` elixir
cannot use ^variable_name outside of match clauses
```

### Get multiple records by attribute and extract or "select" an attribute

#### Keyword Method Bindingless

``` elixir
query = from(User, 
    where: [first_name: "John"], 
    select: [:job])
Repo.all(query)
```

#### Keyword Method with Binding

This allows u to be called anywhere and have differet outputs determined by `select`:

``` elixir
query = from(u in User, 
    where: u.first_name == "John", 
    select: {u.job, u.age})
Repo.all(query)
```

<!--     User
    |> where(first_name: "John")
    |> select(:job)
    |> Repo.all -->


<!-- 
iex> Movie \
...>  |> where([m], m.id < 2) \
...>  |> select([m], {m.title}) \
...>  |> Repo.all -->


<!-- # The functional method using an expression
User
|> where([u], u.first_name == "John")
|> Repo.all

# The declarative method
query = from u in User,
        where: u.first_name == "John"
query |> Repo.all -->

### Get last n records

<!-- User
|> where([u], not (u.first_name == "John"))
|> Repo.all -->

Pipe 

``` elixir
User
|> where([u], u.first_name != "John")
|> limit(5)
|> Repo.all
```

No Pipe

``` elixir
Repo.all(from u in User, where: u.first_name != "John", order_by: [desc: :inserted_at], limit: 5)
```



### Negation


<!-- User
|> where([u], not (u.first_name == "John"))
|> Repo.all -->

``` elixir
User
|> where([u], u.first_name != "John")
|> Repo.all
```


### Multiple values

<!-- Equivalent to WHERE first_name IN ('John', 'Erin')

User.where(first_name: ["John", "Erin"])
User.where.not(first_name: ["John", "Erin"]) -->

``` elixir
User 
|> where([u], not (u.name in ["John", "Erin"]))
|> Repo.all

User 
|> where([u], u.name in ["John", "Erin"])
|> Repo.all
```


<!-- User 
|> where([u], not (u.name in ["John", "Erin"]))
|> Repo.all -->

### Comparisons

<!-- ActiveRecord has two schools of thought here:

    Parameterized strings
    Arel

# You either did this
User.where('score >= ?', 100)
# Or this
uarel = User.arel_table
User.where(uarel[:score].gteq(100))

but it's a lot more pleasant looking on the other side. -->

``` elixir
User
|> where([u], u.score >= 100)
|> Repo.all
```

### OR statements

``` elixir
User
|> where(first_name: "John")
|> or_where(first_name: "Jack")
|> Repo.all
```

### Sorting

``` elixir
User
|> order_by(:first_name)
|> Repo.all

User
|> order_by(desc: :first_name)
|> Repo.all

User
|> order_by([:last_name, :first_name, desc: :age])
|> Repo.all
```


### Pagination (Offset/Limit)

``` elixir
User
|> order_by(:first_name)
|> limit(5)
|> offset(10)
|> Repo.all
```

### Loading 

``` elixir
User 
|> Repo.get(1)
|> Repo.preload([:addresses])

User
|> Repo.get(1)
|> Repo.preload([addresses: order(Address, :state)])
```


### Grouping

``` elixir
User
|> group_by(:first_name)
|> select([:first_name])
|> Repo.all
```


### Aggregates

``` elixir
User
|> Repo.aggregate(:count, :id)

User
|> Repo.aggregate(:count, :first_name)

User
|> group_by(:first_name)
|> select([u], [u.first_name, count(u.id)])
```