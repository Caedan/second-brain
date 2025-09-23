---
id: box-smart-pointer
aliases: []
tags:
  - rust
  - pointers
---

# Box Smart Pointer

> Box smart pointers give you single ownership of something stored on the Heap

Some of the use cases for Box smart pointers would be:

- to avoid copying large amounts of data , when transferring ownership
- In combination with trait objects
- When you have a type of an unknown size, and you want to use it in a context
where the exact size is required (e.g. recursive types)

```rust
struct Button {
	text: String
}

impl UIComponent for Button {}

// recursive types
struct Container {
	name: String,
	child: Box<Container>,
}

impl UIComponent for Container {}

fn main() {
	let button_a = Button { text: "button a".to_owned() };
	let button_b = Box::new(Button { text: "Some heavy ass Button".to_owned() });

	// copying data
	let button_c = button_a;
	let button_d = button_b;
	
	// Combining trait objects
	let components: Vec<Box<dyn UIComponent>> = vec![
		Box::new(button_c),
		button_d
	];
	
}
```
