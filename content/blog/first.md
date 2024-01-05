---
title: "A Practical Taste of Procedural Macros in Rust"
date: 2023-12-28
---

## What are procedural macros?

Procedural macros in rust are macros that take code as an input. An example of a procedural macro is the `PartialEq` macro which allows equality checks.

```rust
#[derive(PartialEq)]
struct Person {
    id: u8,
    name: String,
}

fn main() {
    let john = Person { id: 0, name: "John".into() };
    let sarah = Person { id: 1, name: "Sarah".into() };
    assert_neq!(john, sarah);
}
```

In this post, I will explain how to make a simple procedural macro that counts the number of fields/variants in a struct, enum, or union. 

## Project setup

The first step is to create our project with `cargo new --lib num_fields`. Due to complicated constraints going deep into the language, we need to mark this crate (`num_fields`), as a crate with a procedural macro. To do this we write the following in `Cargo.toml`:

```toml
[lib]
proc_macro = true
```

> **Note**: In most crates, the proc macro will be in a sub crate

Next, install `syn` and `quote` with `cargo add syn quote`. 

## Our Code

In `src/lib.rs`, delete everything and write:

```rust
#[proc_macro_derive(NumFields)]
pub fn num_fields_derive(input: TokenStream) -> TokenStream { ... }
```

This creates a proc macro for the `NumFields` trait. The first thing we need to do is to parse the input into something easier to work with, in this case, `syn`'s `DeriveInput` struct:

```rust
let input = parse_macro_input!(input as DeriveInput);
```

Next, we need to get the name of the struct/enum/union we are working with:

```rust
let mame = input.ident;
```

Next, we match the type of the data we are working on: 

```rust
let num_fields = match input.data {
        Data::Enum(enum_data) => enum_data.variants.len(),
        Data::Union(union_data) => union_data.fields.named.len(),
        Data::Struct(struct_data) => struct_data.fields.len(),
};
```

Finally, we put together the output with the `quote!` macro:

```rust
quote! {
    impl #name {
        const NUM_FIELDS: usize = #num_fields;
    }
}.into()
```

All in all, the function looks like this:

```rust
#[proc_macro_derive(NumFields)]
fn num_fields_derive(input: TokenStream) -> TokenStream {
    let input  = parse_macro_input!(input as DeriveInput);

    let name = input.ident;

    let num_fields = match input.data {
        Data::Enum(enum_data) => enum_data.variants.len(),
        Data::Union(union_data) => union_data.fields.named.len(),
        Data::Struct(struct_data) => struct_data.fields.len(),
    }

    quote! {
        impl #name {
            const NUM_FIELDS: usize = #num_fields;
        }
    }.into()
}
```

## Testing

In order to test our macro, from the root of the project write `cargo new example`. 

Inside of example, under dependencies, write `num_fields = {path=".."}`. 

In `src/main.rs`, write:

```rust
use num_fields::NumFields;

#[derive(NumFields)]
enum TestEnum {
    Good,
    Bad,
    Ugly,
}


#[derive(NumFields)]
union TestUnion {
    id: u8
}

#[derive(NumFields)]
struct TestStruct([u16;8]);

fn main() {
    println!("Enum Fields: {}", TestEnum::NUM_FIELDS);
    println!("Union Fields: {}", TestUnion::NUM_FIELDS);
    println!("Struct Fields: {}", TestStruct::NUM_FIELDS);
}
```

If you run the example, the results should be as expected.
