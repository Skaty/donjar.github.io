---
layout: post
mathjax: false
date: 2017-12-03
title: "The Array of Options/Optionals"
categories: computer-science functional-programming
---

This is a pattern that I saw again and again that I decided to make a quick post
about it.

## Background
Consider this problem:

> Given a String, split it on spaces and parse each part into an integer. If a
> part is not an integer, omit it from the result.

For example, `'1 2 abc -3'` becomes `[1, 2, -3]`.

## Initial Solution
Here's a quick solution in Java:
```java
public ArrayList<Integer> parseIntegerArray(String input) {
    ArrayList<Integer> result = new ArrayList<Integer>();

    for (String i : input.split(" ")) {
        try {
            result.add(Integer.parseInt(i));
        } catch (NumberFormatException e) {
            // Parse fail, do nothing
        }
    }

    return result;
}
```

However, since we are working with data, usually there is a quick way to do it
in functional programming. Indeed, assuming that all parts are integers, we can
do it in one line in Ruby:
```ruby
def parse_to_integer_array(input)
  input.split(' ').map(&:to_i)
end
```

(By the way, `map(&:to_i)` is the same as `map { |s| s.to_i }`, and `to_i`
basically means "to integer".)

Now what if there are non-integer parts? The `#to_i` method will return 0 here
(which sucks).

In more modern languages, there are constructs that can still do this in one
line. Before that, let's talk about something else...

## Optionals
If you have worked with Java, you must have known about the annoying
`NullPointerException`, which basically what happens when you try to call
methods/attributes on a `null` value.

To solve this, modern languages have a type called the Option type (sometimes,
the Optional type). Basically, it is either a None (representing a null value),
or a Some (representing the value).

The integer parsing above is an example. We would like to have `parse("3")` to
return `Some(3)` and `parse("-12")` to return `Some(-12)`, for example. Giving
invalid input, for example `parse("abc")`, will return `None` instead.

You can read more about Option types at
[this Wikipedia link here](https://en.wikipedia.org/wiki/Option_type).

Sample implementations in languages include:
- Swift: [the Optional type](https://developer.apple.com/documentation/swift/optional)
- Scala: [the Option type](https://www.scala-lang.org/api/current/scala/Option.html)
- Rust: [the Option type](https://doc.rust-lang.org/std/option/index.html)

## Going back to the problem
Now, if we do something like our `map` solution, we will get an array of
Options. For example, in Swift, we can do something like:
```swift
func parseToIntegerArray(input: String) -> [Int] {
  input.split(separator: " ").map { Int($0) }
}
```
and running `parseToIntegerArray("2 a 3")` will return `[2, nil, 3]`. Now, how
do we remove the `nil` values?

One way to do it is by using `array.filter { $0 != nil }`. However, there is a
neater solution here...

## The "flat map" function
In most libraries that support some notion of "higher order functions", one can
usually find methods like `map`, `reduce` (or `fold`, `inject`, etc), `filter`,
`flatten`, `for_each`, and so on. Another common method is `flat_map`, which
flattens the array after mapping them.

For example, let's say we have an array of people: `["John Doe", "Luke Walt"]`.
Suppose we have a function which returns the children of a person. Calling the
function on this array will produce something like, for example,
`[["Jane Doe", "Jake Doe"], ["Nancy Walt", "Troy Walt", "Alice Walt"]]`.
However, if suppose we want to get an array of strings, we can use `flat_map`
instead of `map`, we can get
`["Jane Doe", "Jake Doe", "Nancy Walt", "Troy Walt", "Alice Walt"]` instead.

I personally have known this method for quite some time. What I didn't know,
though, is that we can treat an Option type as an array: either consisting of
one element (for `Some`), or zero elements (for `None`)!

Most languages also implement `flat_map` to expand these Option types. Going
back to the string parsing example, we have these working solutions:

### Swift Solution
```swift
func parse(input: String) -> [Int] {
  return input.split(separator: " ").flatMap { Int($0) }
}
```

Nothing special over here. Note that we are using the `Int` constructor here,
which actually returns `Int?` (the option type for integers). In Swift, this is
called "failable constructors" - constructors that can fail.

### Scala Solution
```scala
import scala.util.Try

def parse(input: String) = input.split(" ").flatMap(s => Try(s.toInt).toOption)
```

The code might look quite complicated, but `Try(s.toInt).toOption` basically
just converts all exceptions to a `None`.

### Rust Solution
```rust
fn parse(input: &str) -> Vec<i32> {
  input.split(" ").filter_map(|s| s.parse().ok())
}
```

Notice that in Rust, we use the special function `filter_map` instead. (Took me
two hours to figure this out ugh)

## Afterthoughts
I first saw this problem when I was doing my internship in Twitter. Basically,
I have a pipeline of data that I would like to process. However, some of these
data are anomalies which need to be discarded. Using Scala, all I need to do is
to use the `flatMap` function.

I saw this again when I was learning Rust on
[Advent of Code 2017 Day 2](https://adventofcode.com/2017/day/2). Even though
the sample data given consists of all numbers, I was wondering if I can remove
all bad input from the data. Which brings us back to our problem here!

To sum it up: if you need to process data that might have bad data you wish to
discard, check out the `flatMap` function from your programming language's array
library. (If you can't find them, try to read through all methods, just like
what I did in Rust!)
