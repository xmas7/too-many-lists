% A Safe Doubly-Linked Deque

# This Chapter is Under Construction And I Didn't Feel Like Making a Git Branch

Now that we've seen Rc, and heard about interior mutability, this gives an
interesting thought... maybe we *can* mutate through an Rc. And it *that's*
the case, maybe we can implement a *doubly* linked list totally safely!

In the process we'll become familiar with *interior mutability*, and probably
learn the hard way that safe doesn't mean *correct*. Doubly linked lists are
hard, and I always make a mistake somewhere.

Let's add a new file called `fourth.rs`:

```
// in lib.rs

pub mod first;
pub mod second;
pub mod third;
pub mod fourth;
```

This will be another clean-room operation, though as usual we'll probably find
some logic that applies verbatim again.

