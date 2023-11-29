[Back to README.md](README.md)


# `CellZomelets` Instance
Consists of 2 configuration elements

1. Interfaces - *(required)* the map of Zomelets
2. Transformers - *(optional)* input/output functions


### `constructor( interfaces, transformers )`

Both elements can be defined in the first argument using the `CellZomeletsConfig` struct;
destructuring is triggered if `arguments[0]` has the key `interfaces` with type of `object` (rather
than an instance of `Zomelet` or a `ZomeletConfig`).  When this is triggered, `arguments[1+]` are
ignored.

- `interfaces` - *(required)* an object of functions
  - Should throw an error if any value is not an instance of `Zomelet` or a `ZomeletConfig`.
- `transformers` - *(optional)* a list of `TransformerConfig` struct


#### `CellZomeletsConfig` Struct

```js
{
    interfaces: Object<String, Zomelet | ZomeletConfig>,
    transformers: Option<Vec<Transformer | TransformerConfig>>,
}
```

##### What if there is a zome named `interfaces`; how do we know it's not a `CellZomeletsConfig`?
To make this deterministic, we have to eliminate support for defining a Zomelet using the functions
configuration.

For example, if a `CellZomeletsConfig` had a zome named `functions`, and it tried to use the functions
configuration input, we would not be able to determine if the `functions` key indicates a
`ZomeletConfig`, or a `CellZomeletsConfig`.

Example of an **invalid** definition
```js
{
    interfaces: {
        // zome name => method configuration
        functions: {
            async get_thing ( id ) {
                // ...
            },
        },
    },
}
```

Example of a **valid** definition
```js
{
    interfaces: {
        // zome name => ZomeletConfig
        functions: {
            functions: {
                async get_thing ( id ) {
                    // ...
                },
            },
        },
    },
}
```

For consistency, we also don't allow using method configurations in the interfaces input
(`arguments[0]`).

Example of **invalid** definition
```js
{
    // zome name => method configuration
    interfaces: {
        async get_thing ( id ) {
            return new Thing( await this.call( id ) );
        },
    },
}
```

Although we could determine that the above structure is not a `CellZomeletsConfig` (because the
values are of type `function`), it would make the `CellZomeletsConfig` input inconsistent.  So for
that reason we don't support defining a Zomelet using just the functions configuration in a
`CellZomelets` constructor.


### State

#### `<CellZomelets>.zomes`
Map of expected zome interfaces.  Where the key is the zome name and the value is a `Zomelet`
instance.


### Transformers

#### `<CellZomelets>.processInput( input )`
Run all input transformers in the reverse order they were defined.


#### `<CellZomelets>.processOutput( output )`
Run all output transformers in the order they were defined.



## Interfaces

Example
```js
{
    some_zome: SomeZomelet,
    other_zome: {
        functions: {
            async get_thing ( id ) {
                return new Thing( await this.call( id ) );
            },
        },
    },
}
```



## Transformers
See [Transformer.md](Transformer.md)


### `input` Structure

```js
[
    zome : String,
    func : String,
    args : Option<Any>,
    options : Option<Number | Object>,
]
```

Example
```js
{
    async input ([ zome, func, args, options ]) {
        return [ zome, func, args, options ];
    },
}
```
