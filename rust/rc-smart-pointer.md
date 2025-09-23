---
id: rc-smart-pointer
aliases: []
tags:
  - rust
  - pointers
---

# RC Smart Pointer

The Rc stands for **R**eference **C**ounting
and this pointer helps us to share ownership between multiple resources.
Unlike the `Box` smart pointer, the `Rc` smart pointer is not
included in the Rust prelude; so we have to import it.

```rust
use std::rc::Rc;

struct Database {}

struct AuthService {
	db: Rc<Database>
}

struct ContentService {
	db: Rc<Database>
}

fn main() {
	let db = Rc::new(Database {});
	let auth_service = AuthService { db: Rc::clone(&db) };
	let content_service = ContentService { db: Rc::clone(&db) };
}
```

> The `Rc` smart pointer can only be used in single-threaded applications.
> In multi-threaded applications you need to use
> the atomically reference counted smart pointer.
>
> If you need the data inside the Rc pointer to be mutable and shared,
> you need to use a `RefCell` pointer in combination with the `Rc` pointer
>
> `RefCell` tells the Rust compiler to ignore
> any violations against the borrowing rules.
> However, they're still enforced at runtime
> if you try to use 2 mutable references for example.
