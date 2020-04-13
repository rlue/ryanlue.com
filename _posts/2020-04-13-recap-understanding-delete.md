---
title: "Reading Recap: Understanding Delete"
teaser: A condensed summary of Juriy Zaytsev’s 2010 write-up
category: recaps
tags: [javascript, language-internals]
---

From <https://perfectionkills.com/understanding-delete/> by Juriy Zaytsev (2010)

`delete` is a built-in JS operator for **removing properties from objects**:

```javascript
var foo = { bar: 'baz' }
delete foo.bar  // true
foo             // {}
```

There’s a lot of misinformation floating around on the Internet about what it
is and how it works. In _Object-Oriented JavaScript,_ Stoyan Stefanov even
mistakenly writes that it can be used to delete bare functions:

```javascript
function foo() { console.log('bar') }  // AUTHOR SAYS | ACTUALLY RETURNS
delete foo                             // true        | false
typeof foo                             // undefined   | function
```

So what’s the confusion all about?

### Background

In JS, all code is run within an **execution context**. This determines what
variables are accessible at any given time. Usually, when you declare and use
variables or functions, they appear to exist on their own:

```javascript
var foo = 1
foo  // 1

function bar() { return 1 }
bar()  // 1
```

but in reality, they are defined as properties on the **global object** (in
global code) or **activation object** (in function code):

```javascript
// NOTE: this does not apply for const and let
var foo = 1
typeof this.foo  // 'number'

function bar() { return 1 }
typeof this.bar  // 'function'
```

Within functions, this object is also where function parameters (and the
`arguments` variable) are stored.

For a comprehensive take, see [this lesson][excons] from [Tyler McGinnis’
_Advanced JavaScript_ course][tmg]. (Side note: can’t recommend his courses
enough. Worth every penny.)

### How `delete` Really Works

`delete` doesn’t operate on _variables;_ it operates on _object properties._
Under the hood, object properties have certain flags set on them at the time
they are defined; one such property is `DontDelete`. You can probably guess
what it does.

`DontDelete` is only disabled when 1) a property is explicitly assigned or 2)
a variable is set via undeclared assignment:

```javascript
var foo = 1           // DontDelete
function foo()        // DontDelete
this.foo = 1          // !DontDelete
foo = 1               // !DontDelete
var foo.bar = 1       // foo: DontDelete / bar: !DontDelete
var foo = { bar: 1 }  // foo: DontDelete / bar: !DontDelete
```

In practice, browsers have all sorts of quirks when implementing this
behavior.

### Why _Object-Oriented JavaScript_ Was Mistaken

Presumably, the author was using Firebug to confirm his findings. Firebug runs
all code in an `eval` execution context, where even more inconsistencies
abound!

[excons]: https://www.youtube.com/watch?v=Nt-qa_LlUH0
[tmg]: https://tylermcginnis.com/courses/advanced-javascript/
