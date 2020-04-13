---
title: What Happens When Variables Point to Each Other?
teaser: A look at object mutability & ‘pass by reference’ vs. ‘pass by value’, and how they affect the behavior of variables in Ruby.
category: fundamentals
tags: [ruby, language-internals]
---

In programming, [conciseness is a virtue][sip]{:target="_blank"}. But back when I was first learning to program in Ruby (which was not all that long ago), I would occasionally discover that the concisest way of doing something was not actually the correct way to do it – that, due to some unforeseen aspect of the language’s design, I would try to simplify a set of instructions and wind up with something entirely different from what I had intended. 

One case that comes to mind is when I tried to initialize two variables as empty arrays, and then populate each one separately:

~~~ ruby
>> team_usa = team_uk = []
=> []
>> team_usa.push('Benedict Arnold')
=> ["Benedict Arnold"]
>> team_uk
=> ["Benedict Arnold"]  # How’d you get over here?
~~~

To understand why this happens, we need to look under the hood at how Ruby (or any other programming language) stores data and passes it around.

Object (Im)mutability
---------------------

<dfn>Variables</dfn> are the handles by which we grasp and manipulate data in programming languages. They’re not _containers_ of data, but rather _labels_ for such containers. Not to split hairs here, but there’s an important distinction to be made: variables themselves do not hold the data we assign them to, but rather hold **reference values** that point to **objects** in computer memory (where the data is really stored).

Every `Object` in Ruby has an `#object_id` – a string of numbers which acts as a sort of unique address for that object in computer memory. Sometimes, when we change a variable, we’re directly altering the object that it points to – same object, new data: 

~~~ ruby
>> str = 'butter'
=> "butter"
>> str.object_id
=> 70221402510360  # <- Note how this value...
>> str << 'face'
=> "butterface"
>> str.object_id
=> 70221402510360  # <- ...does not change.
~~~

Other times, we can’t change the underlying object, so we have to take the variable and assign it to a _new_ one:

~~~ ruby
>> a = 1
=> 1
>> a.object_id
=> 3  # <-------------- But this value...
>> a += 1
=> 2
>> a.object_id
=> 5  # <-------------- ...does.
~~~

Why the inconsistency? In Ruby, some objects are **mutable** by default (such as strings, arrays, and hashes), which means that each time one is created (either with the `::new` method or a `''` / `[]` / `{}` literal), it gets a new object id assigned to it. 

~~~ ruby
>> 'hello'.object_id
=> 70221390700440
>> 'hello'.object_id
=> 70221390129420
>> 'hello'.object_id
=> 70221394833080
>> 'hello'.object_id
=> 70221394747660
>> 'hello'.object_id
=> 70221394590800
~~~

That means that two strings can have the same value but still be different – like identical twins, they look the same on the surface, but each has a life of its own.

Alternately, you can assign two variables to the same string, in which case a single object may have two separate names – more like a secret agent, or a two-timing boyfriend:

~~~ ruby
>> a = b = c = 'hello!'
=> "hello!"

>> a << ' i love you!'             # The ‘shovel’ operator modifies
=> "hello! i love you!"            # an object in place.

>> b += ' kind of!'                # The ‘plus-equals’ operator creates
=> "hello! i love you! kind of!"   # a new object, then points the 
                                   # variable to the new object’s id.
>> c
=> "hello! i love you!"
~~~

Other objects (booleans like `true` and `false`, or any `Fixnum`) are **immutable**. They each have their own predetermined, unchanging object ID. `2` is always the same `2`, no matter where it appears or how it is arrived at.

~~~ ruby
>> 2.object_id
=> 5
>> [1, 2][1].object_id
=> 5
>> '2'.to_i.object_id
=> 5
>> 'hi'.length.object_id
=> 5
>> (20/10).object_id
=> 5
~~~

Mutable objects can be made immutable with the `#freeze` method, but there is no way to take an immutable object and make it mutable.

_(Note: There is talk of [making all strings immutable in Ruby 3.0][tweet]{:target="_blank"}.)_

This all gives rise to another question: When new variables are assigned to existing variables, do the new variables point to the old object IDs (“pass by reference”), or do they point to a new one with the old data copied into it (“pass by value”)? If the former, changes to one variable will result in changes to the other. If not, changes to one variable leave the other untouched. 

So Which Is It?
---------------

In practice, Ruby _mostly_ follows pass-by-reference behavior, with the following caveat: when two variables are assigned to the same object, methods that modify the variable in-place (<i class="foreign">e.g.,</i> `<<`, `#gsub` or `#map!`) will change the value of both variables; but `=` (or any method that descends from it, like `+=`), will break the connection between the variables and assign one to a new object altogether.

Thus, Ruby may be said to have a <dfn>pass by reference-value</dfn> (or pass by object reference) evaluation strategy. Some methods alter the underlying object, while others assign the variable a new reference value.

Suppose you have an array, and you want to change the value of an element _indirectly_ by assigning it to another variable, then modifying that variable:

~~~ ruby
>> calories = [500, 700, 700]
=> [500, 700, 700]
>> lunch = calories[1]
=> 700
>> lunch += 300
=> 1000 
>> calories
=> [500, 700, 700]                 # <- calories[1] didn’t change!
~~~

Why doesn’t this work? The `+=` assignment in line 3 has changed the _address that `lunch` points to_, not the object it originally pointed to.

Since `Fixnum`s are immutable, you can’t just up and change the object `700` to a different value. (There’s only one such `700` Fixnum object in the system, and it already existed in the Ruby environment before we pointed a variable to it, so what would it even mean to alter it?) In order to change the value of `calories[1]`, you need to point it to a different object ID, which can’t be done by altering or reassigning `lunch`. It’s the same for strings – unless, of course, you modify them in-place:

~~~ ruby
>> calories = ['500', '700', '700']
=> ['500', '700', '700']
>> lunch = calories[1]
=> '700'
>> lunch.replace('1000')
=> '1000' 
>> calories
=> ['500', '1000', '700']
~~~

What If You Actually Want a New Object?
---------------------------------------

Now suppose you’re trying to keep a rolling record of everything you’ve eaten since yesterday. You keep this data in two separate arrays, `meals_yesterday` and `meals_today`. So when the next day begins, you copy the one into the other and start updating with today’s menu:

~~~ ruby
>> meals_yesterday = meals_today
=> ["bacon pancakes", "tacos", "pizza"]
>> meals_today.clear
=> []
>> meals_today.push('leftover pizza')
=> ["leftover pizza"]
~~~

What’s wrong with this? If you’ve been following along, you’ll know that `meals_today.clear` emptied _both_ arrays: `meals_today` = `meals_yesterday` = `["leftover pizza"]`.

So what’s the solution? In this particular case, you could simply assign `meals_today` to a new, empty array instead of using `#clear`, but a more general approach is to `#dup` or `#clone` the object you want whenever you assign to it:

~~~ ruby
>> meals_yesterday = meals_today.dup
=> ["bacon pancakes", "tacos", "pizza"]
~~~

This creates a copy of the object, with all the same data stored at a new object ID. (Both methods create <dfn>shallow copies</dfn>, meaning that the arrays themselves are different, but the objects each element points to are the same.)

[sip]: http://www.paulgraham.com/power.html
[tweet]: https://twitter.com/yukihiro_matz/status/634386185507311616?lang=en
