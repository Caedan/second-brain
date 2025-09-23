---
id: project-structure
aliases: []
tags:
  - rust
  - architecture
---

# Project Structure

## Workspaces

## Packages

Packages contain one or more crates that provide a set of functionality.
Packages allow you to build, test, and share crates.
Every package needs a `Cargo.toml` file, which describe the package
and defines how to build crates.

Rules:

- Must have at least 1 crate
- At most 1 library crate
- Any number of binary crates

## Crates

A crate is a tree of modules that produces either a library or an executable.
There are two types of crates: Binary crates and Library crates.

Library crates are for sharing code between other crates,
whilst Binary crates create an executable.

A binary crate has to contain a `main()` function and if we have only one;
then it is convention to call it `main.rs`.
If we have multiple binary crates, then they'll be inside a folder called `bin/`.

## Modules

Modules let you control the organisation, scope, and privacy of your code.
You can declare modules inline or in a separate file like so:

```rust
mod database {
	pub fn connect() {
		// Do some databse connection thing
	}
}

// or you could create a new file called database.rs and include the code
// like so
pub fn connect() {
	// Do some databse connection thing
}
```

If you want to include modules from a subdirectory,
there are two ways you could do this.
You could either create a `mod.rs` file in each subdirectory -
which can get confusing if you have multiple `mod.rs` files open -
or you could create a new file within the `src` directory and name it after the subdirectory.

Example:

- src
        - main.rs
        - database
            - mod.rs
            - models.rs

OR

- src
        - main.rs
        - database.rs
        - database
                - models.rs

> In Rust modules are not mapped to the file system
> like you'd expect in javascript for example.
