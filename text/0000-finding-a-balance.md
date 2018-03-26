- Feature Name: balanced-structure
- Start Date: 2018-04-01
- RFC PR: (leave this empty)
- Rust Issue: (leave this empty)

# Summary
[summary]: #summary

This RFC proposes a new error style that strikes a better balance between structured output and human readability.

# Motivation
[motivation]: #motivation

The current `error-style` option of `rustc` provides three styles of error: `human`, `json` or `short`. When using `human`, the compiler will print errors like this:

```sh
error[E0277]: `Foo` doesn't implement `std::fmt::Debug`
 --> error-message.rs:4:22
  |
4 |     println!("{:?}", Foo);
  |                      ^^^ `Foo` cannot be formatted using `:?`; add `#[derive(Debug)]` or manually implement `std::fmt::Debug`
  |
  = help: the trait `std::fmt::Debug` is not implemented for `Foo`
  = note: required by `std::fmt::Debug::fmt`

error: aborting due to previous error
```

While this form is visually pleasing, it also suffers from some problems: many errors have custom display variants, needing the user to accomodate with a different error structure depending on what the error was.

Users interested in a more structured approach might reach for `json` here, only to figure out that the format is consistent, but far too verbose.

`short` might be perceived as too short, though, as there's almost no additional context other then the error message.

We propose an additional error style that represents an inbetween of the existing approaches. It strikes a balance between structure, human-readability and CLI brevity. Also, it follows Rusts tradition of using ideas from the past to implement the tooling of the future.

# Guide-level explanation
[guide-level-explanation]: #guide-level-explanation

The new error messages of `rustc` all follow a familiar structure, which makes them easy to follow. They do away with the notation-heavy structure that JSON exposes, though.

```
You asked me to `Debug` a `Foo`
alas, I don't know what to do,
for `Foo` has no impl,
it's really that simple,
I'll leave the debugging to you.
```


# Reference-level explanation
[reference-level-explanation]: #reference-level-explanation

This is the technical portion of the RFC. Explain the design in sufficient detail that:

- Its interaction with other features is clear.
- It is reasonably clear how the feature would be implemented.
- Corner cases are dissected by example.

The section should return to the examples given in the previous section, and explain more fully how the detailed proposal makes those examples work.

# Drawbacks
[drawbacks]: #drawbacks

Why should we *not* do this?

# Rationale and alternatives
[alternatives]: #alternatives

- Why is this design the best in the space of possible designs?
- What other designs have been considered and what is the rationale for not choosing them?
- What is the impact of not doing this?

# Prior art
[prior-art]: #prior-art

[Shakespeare](http://shakespearelang.sourceforge.net/)

# Unresolved questions
[unresolved]: #unresolved-questions

- What parts of the design do you expect to resolve through the RFC process before this gets merged?
- What parts of the design do you expect to resolve through the implementation of this feature before stabilization?
- What related issues do you consider out of scope for this RFC that could be addressed in the future independently of the solution that comes out of this RFC?
