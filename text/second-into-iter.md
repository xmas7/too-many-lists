% IntoIter

Collections are iterated in Rust using the *Iterator* trait. It's a bit more
complicated that `Drop`:

```rust
pub trait Iterator {
    type Item;
    fn next(&mut self) -> Option<Self::Item>;
}
```

The new kid on the block here is `type Item`. This is declaring that every
implementation of Iterator has an *associated type* called Item. In this case,
this is the type of this that it can spit out when you call `next`.

The reason Iterator yields `Option<Self::Item>` is because the interface
coallesces the `has_next` and `get_next` concepts. When you have_next, you yield
Some(value), and when you don't you yield None. This makes the
API generally more ergonomic and safe to use and implement, while avoiding
redundant checks and logic between `has_next` and `get_next`. Nice!

Sadly, Rust has nothing like a `yield` statement, so we're going to have to
implement the logic ourselves. Also, there's actually 3 different kinds of
iterator each collection should endeavour to implement:

* IntoIter - `T`
* IterMut - `&mut T`
* Iter - `&T`

We actually already have all the tools to implement
IntoIter using List's interface: just call `pop` over and over. As such, we'll
just implement IntoIter as a newtype wrapper around List:


```rust
// Tuple structs are an alternative form of struct,
// useful for trivial wrappers around other types.
pub struct IntoIter<T>(List<T>);

impl<T> List<T> {
    pub fn into_iter(self) -> IntoIter<T> {
        IntoIter(self)
    }
}

impl<T> Iterator for IntoIter<T> {
    type Item = T;
    fn next(&mut self) -> Option<Self::Item> {
        // access fields of a tuple struct numerically
        self.0.pop()
    }
}
```

And let's write a test:

```rust
#[test]
fn into_iter() {
    let mut list = List::new();
    list.push(1); list.push(2); list.push(3);

    let mut iter = list.into_iter();
    assert_eq!(iter.next(), Some(3));
    assert_eq!(iter.next(), Some(2));
    assert_eq!(iter.next(), Some(1));
}
```

```text
> cargo test
   Compiling lists v0.1.0 (file:///Users/ABeingessner/dev/too-many-lists/lists)
     Running target/debug/lists-5c71138492ad4b4a

running 3 tests
test first::test::basics ... ok
test second::test::basics ... ok
test second::test::into_iter ... ok

test result: ok. 3 passed; 0 failed; 0 ignored; 0 measured

   Doc-tests lists

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured
```

Nice!

