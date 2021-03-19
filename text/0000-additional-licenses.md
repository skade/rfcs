- Feature Name: additional_licences_field
- Start Date: (fill me in with today's date, YYYY-MM-DD)
- RFC PR: [rust-lang/rfcs#0000](https://github.com/rust-lang/rfcs/pull/0000)
- Rust Issue: [rust-lang/rust#0000](https://github.com/rust-lang/rust/issues/0000)

# Summary
[summary]: #summary

Extend `Cargo.toml` with a field that declares additional licenses that need to be taken into account when using the crate in question on a product. Especially `-sys` crates may have the binding code licensed under a different license than the code bound to. While this is rarely an incompatibility concern, this is hard to track with automated tooling. This RFC proses an additional field that lists additional licenses to take into account when using the library.

# Motivation
[motivation]: #motivation

It is legal and common to write wrapping libraries around other libraries that have a different, but compatible, license. There's multiple legitimate motivations for that, among them the ability to exchange the wrapped library for another one later while staying aligned with the license of library clients. In general, Cargo makes it easy to stay compliant as a user, by reading crates metadata. This does not extend to situations where a crate binds to one or multiple libraries from other ecosystems, for example static or dynamic libraries. The form of binding does not matter, as many licenses express expectations on _distribution_ of the final program.

# Guide-level explanation
[guide-level-explanation]: #guide-level-explanation


For complex cases, Cargo manifests provides additional fields for licensing information. These are useful for example if you bind to one of multiple libraries that Cargo does not see as dependencies and not access metadata for. Both are subfields of a `licensing` field. The `licensing` section also provides a `license` field that acts as the top-level `license` if present. Both using `licensing.license` and `license` is an error.

* `licensing.additional`: An key value element that describes additional licenses to be taken into account. The key is the common name of the license bound to, the value a K/V element with the `license` and `optional` fields. `license` is an SPDX identifiers, `optional` describes if a license can be ignored in some cases.

* `comment`: A free form string to describe additional licensing detail. This can be useful if e.g. a library can bind to multiple backends of different licenses and a choice exists. This field should only be used if really necessary, as audit software is recommended to trigger a manual review.

Example:

```toml
[licensing]
license = "MIT"
comment = "When using the `native-backtrace` feature, you need to also comply with the libbacktrace license"
[licensing.additional]
libbacktrace = { license: "BSD-3-Clause", optional = true }
```
# Reference-level explanation
[reference-level-explanation]: #reference-level-explanation


The two fields `licenses.additional` and `licenses.comment` are added to Cargo as plain metadata fields. They require the presence of a `license` field. Both fields will not be warned against if they are missing.

Cargo only checks those fields for structural validity, but does not further use them. Tools like `cargo-license` are encouraged to present this data alongside with the normal licensing data and highlight if a comment is present.

# Drawbacks
[drawbacks]: #drawbacks

This proposal tries to strike a balance between ease of use and information, but for that reason e.g. lacks
the ability to declare certain licenses only if certain features are activated.

This proposal adds an additional stuctured data section to `Cargo.toml`, seperate from the general `license` field
in itself and partially superseding it.

# Rationale and alternatives
[rationale-and-alternatives]: #rationale-and-alternatives


* We could just add 2 optional fields to the top-level.

# Prior art
[prior-art]: #prior-art


Perl CPAN modules declare that multiple licenses being named in their licenses field should be interpreted as "consult the documentation for interpretation" (https://perldoc.perl.org/CPAN::Meta::Spec#license).

# Unresolved questions
[unresolved-questions]: #unresolved-questions

* It may be possible to warn against these fields if `build.rs` is present, as it is often used for compiling external libraries.
* The names and exact formats are very much bikesheds.

# Future possibilities
[future-possibilities]: #future-possibilities

The introduction of a `license` section allows for further extension.