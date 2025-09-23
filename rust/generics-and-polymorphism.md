---
id: generics-and-polymorphism
aliases: []
tags:
  - rust
  - generics
  - polymorphism
---

# Generic & Polymorphism

There is no runtime performance cost to using generics in Rust. This is because of a process called monomorphisation. At compile time Rust takes a generic function an creates functions with concrete types.

## Traits

In order to take advantage of Polymorphism and prevent code duplication;
we use Traits to provide a common interface.
If you're coming from an OOP language, then you'd probably see some similarity between
Traits and Interfaces.

```rust
trait Park {
	fn park(&self);
}

struct Car {
	make: String,
	model: String,
	year: u16,
}

impl Park for Car {
	fn park(&self) {
		println!("parking car!");
	}
}

struct Truck {
	make: String,
	model: String,
	year: u16
}

impl Park for Truck {
	fn park(&self) {
		println!("parking truck!");
	}
}
```

> [!info]
> Polymorphism allows us to call methods on an interface without worrying about
the concrete types that implement that interface
