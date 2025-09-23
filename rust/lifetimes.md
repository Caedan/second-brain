---
id: lifetimes
aliases: []
tags:
  - rust
  - memory
---

# Lifetimes

## Concrete Lifetimes

> A concrete lifetime is the time during which a value exists
at a particular memory location.

A lifetime starts when a resource is created or moved into a memory location,
and ends when a resource is dropped or moved out of a memory location.

```rust
fn main() {
	let s1 = String::from("Let's get Rusty!");
	println!("s1: {s1}");
}
```

Looking at the example above, you can see that the string  `s1`
is created on line 2 and cleaned up on line 4.

## Generic Lifetimes

> Lifetime specifiers, otherwise known as generic lifetime annotations are a way to describe the relationship between lifetimes of references (or in other words, the relationship of concrete lifetimes)

Rust would throw a `missing lifetime specifier` error for the following code:

```rust
fn main() {
	let player1 = String::from("player 1");
	let player2 = String::from("player 2");

	let result = first_turn(player1.as_str(), player.as_str());
	println!("Player going first is: {}", result);
}

fn first_turn(p1: &str, p2: &str) -> &str {
	if rand::random() {
		p1
	} else {
		p2
	}
}
```

The way to fix that is by introducing a generic lifetime annotation.
Just like regular generics you declare them in angular bracket `<>` with an apostrophe
at the end of the function name:

```rust
fn first_turn<'a>(p1: &'a str, p2: &'a str) -> &'a str
```

In the code above, we say that there is a relationship between `p1`, `p2`
and the return value. The relationship is this:

> The lifetime of the return value is gonna be equal
> to the shortest lifetime passed in.

If `p1` has a shorter lifetime than `p2`,
than the lifetime of the return value is gonna be equal to `p1`.
Conversely, if the lifetime of `p2` is shorter than the lifetime of `p1`,
than the lifetime of the return value is equal to the lifetime of `p2`.

> You could name your lifetime however you want.
> The convention is to start with a lower case `'a`
> and go down the alphabet from there

## Lifetime Elision

The Rust compiler follow 3 lifetime elision rules:

1. Each parameter that is a reference gets its own lifetime parameter
2. If there is exactly one input lifetime parameter,
that lifetime is assigned to all output lifetime parameters
3. If there are multiple input lifetime parameters,
but one of them is &self or &mut self,
the lifetime of self is assigned to all output lifetime parameters

```rust
struct Tweet<'a> {
	content: &'a str,
}

impl<'a> Tweet<'a> {
	fn replace_content(&mut self, content: &str) -> &str {
		let old_content = self.content;
		self.content = content;
		old_content
	}
}

let main() {
	let mut tweet = Tweet {
		content: "example",
	};
	let old_content = tweet.replace_content("replace_example");
	println!("{old_content}");
	println!("{}", tweet.content);
}
```
