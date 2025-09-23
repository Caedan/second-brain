---
id: memory-management
aliases: []
tags:
  - rust
  - memory
---

# Memory Management

|            | Contents                                                                                                                     | Size                        | Lifetime                 | Cleanup                              |     |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------- | --------------------------- | ------------------------ | ------------------------------------ | --- |
| **Stack**  | Function arguments, local variables, known size at compile time                                                              | Dynamic / Fixed upper limit | Lifetime of the function | Automatic, when the function returns |     |
| **Heap**   | Values that: live beyond a function's lifetime, accessed by multiple threads, are large, are of unknown size at compile time | Dynamic                     | Determined by programmer | Manual                               |     |
| **Static** | Program's Binary, static variables, String literals                                                                          | Fixed                       | Lifetime of program      | Automatic or when program terminates |     |

> The Lifetime and cleanup for Static and Heap is pre-determined,
> whilst the programmer is responsible for managing memory stored on the Heap

## RAII and OBRM

> Manually managing resources is error prone and ownership is ambiguous

```c++
class Car {};

void memory_example()
{
	Car* car = new Car; // allocate memory on the heap
	function_that_can_throw(); // memory leak if exception is thrown
	if(!should_continue()) return; // memory leak if early return
	delete car; // clean up memory on the heap. Note: Potential double free error if car gets deleted twice
}
```

### Resource Acquisition is Initialisation

> A technique / pattern / best practice
> for exception-safe resource management in C++.

Resources are managed through objects.
Objects have constructors which are responsible for creating
and cleaning up the resource safely like so:

```c++
class CarManager
{
	private:
		Car* p; //pointer to a Car
	public:
		CarManager(Car* p) : p(p) {}
		~CarManager()
		{
			// clean up memory on the heap
			delete p;
		}
};

class Car {};

void memory_example_2()
{
	CarManager car = CarManager(new Car);
	function_that_can_throw();
	if(!should_continue()) return;
}
```

However, `memory_example_3()` can be written even better
by making use of the `unique_ptr` from the C++ standard library.
This will take care of cleaning up the memory stored on the Heap:

```c++
void memory_example_2()
{
	unique_ptr<Car> car = make_unique<Car>();
	function_that_can_throw();
	if(!should_continue()) return;
}
```

You can't use another `unique_ptr` to point to the same resource.
This is because a `unique_ptr` represents one single owner of a resource.

You can, however, use a `shared_ptr` as they'll allow you to use a shared resource
by using reference counting. When the first `shared_ptr` is created,
the reference count is 1.
Once the second one is created, the reference count will be 2.

```c++
void memory_example_3()
{
	shared_ptr<Car> car = shared_ptr<Car>();
	shared_ptr<Car> car2 = car;
	function_that_can_throw();
	if(!should_continue()) return;
}
```

### Ownership Based Resource Management (OBRM)

OBRM is based off RAII, but instead of it being a best-practice,
it's a feature built into the Rust language.
The ownership rules - which are checked at compile time - are as follows:

- Each value in Rust has a variable that's called its owner
- There can only be one owner at a time
- When the owner goes out of scope, the value will be dropped

> By the owner I mean who/what is responsible for cleaning up the resource 
>(e.g the current function, the caller of the current function)
>
> **What is a resource?**
> Something with a finite supply that requires management
> Example: Memory allocated on the Heap.
> The heap has a finite supply of memory
> and developers must manage allocations and deallocations.
> Other Examples: network sockets, file handles, Database handles, Mutexes, etc

#### Borrowing

> There can only be one mutable references **or**
> many immutable references **at the same time**

```rust
fn main(){
	let mut s1 = String::from("Rust");
	let r1 = &s1; // borrowed as immutable
	let r2 = &mut s1; // cannot borrow as mutable, because of the above line
	print_string(r1);
	let s1 = add_to_string(s1);
	println!("s1 is: {s1}");
}

fn add_to_string(mut p1: String) -> String {
	p1.push_str(" is awesome")
	p1
}

fn print_string(p1: &String) {
	println!("{p1}");
}
```

The above code can easily be fixed by pushing the `print_string` statement
up by one line.
Additionally, we have to change the `add_to_string` function
to take in a `&mut String` instead.

```rust
fn main(){
	let mut s1 = String::from("Rust");
	let r1 = &s1;
	print_string(r1);
	let r2 = &mut s1;
	add_to_string(r2);
	println!("s1 is: {s1}");
}

fn add_to_string(mut p1: &mut String) { // no return value needed anymore
	p1.push_str(" is awesome") // notice that derefencing happens automatically
}

fn print_string(p1: &String) {
	println!("{p1}");
}
```

The reason why this works, is because `print_string` will cleanup the reference
after its use within the function body.
Since the immutable reference got cleaned up before the mutable reference got created;
it remains within the rules of the borrow checker.

### Dangling References

Another thing you need to be aware of is dangling references.
The following example illustrates how you could end up in said scenario:

```rust
fn generate_string() -> &String {
	let s = String::from("Ferris");
	&s
} // s is dropped
```

Because `s` is cleaned up at the end of the `generate_string` function.
You'll end up with a reference pointing to a cleaned up resource.
Thankfully Rust catches dangling references at compile time!

> As a general rule, if you create an owned value inside a function
> and you want to return the value; You have to move ownership!

#### Move Ownership

Let's say you create a new resource in a function/method block
and need to use it outside of it's current scope.
You could move ownership by returning an owned type like so:

```rust
struct Car {
	make: String,
	model: String,
	year: u16,
}

impl Car {
	fn new(make: String, model: String, year: u16) -> Self {
		Car {
			make,
			model,
			year,
		}
	}
}
```
