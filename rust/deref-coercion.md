---
id: deref-coercion
aliases: []
tags: []
---

# Deref Coercion

Deref coercion only works on types that implement
the `Deref` and `DerefMut` trait.

```rust
use std::ops::{Deref, DerefMut}

struct MySmartPointer<T> {
	value: T
}

impl<T> MySmartPointer<T> {
	fn new(value: T) -> MySmartPointer<T> {
		MySmartPointer { value }
	}
}

impl<T> Deref for MySmartPointer<T> {
	type Target = T;
	fn deref(&self) -> &mut Self::Target {
		&mut self.value
	}
}

impl<T> DerefMut for MySmartPointer<T> {
	type Target = T;
	fn deref(&self) -> &mut Self::Target {
		&mut self.value
	}
}

fn main() {
	let s = MySmartPointer::new(Box::new("Hello World".to_owned()));
	// &MySmartPointer -> &Box -> &String -> &str
	let s: &str = &(***s);
	print(s);
}

fn print(s: &str) {
	println!("{s}");
}
```

It's not necessary to perform the deref coercions manually (e.g `***s`).
The compiler will dereference the arguments accordingly;
As long as the required datatype is inside the pointer.

> [!info]
> Dereferencing happens at compile time
