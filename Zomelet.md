[Back to README.md](README.md)


# `Zomelet` Instance
Consists of 3 configuration elements

1. Functions - *(required)* the map of functions
2. Dependencies - *(optional)* expected zome/cell/virtual interfaces
3. Transformers - *(optional)* input/output functions


### `constructor( functions, dependencies, transformers )`

All three elements can be defined in the first argument using the `ZomeletConfig` struct;
destructuring is triggered if `arguments[0]` has the key `functions` with type of `object` (rather
than type of `function`).  When this is triggered, `arguments[1+]` are ignored.

- `functions` - *(required)* an object of functions
- `dependencies` - *(optional)* a `DependencyConfig` struct
- `transformers` - *(optional)* a list of `TransformerConfig` struct


#### `ZomeletConfig` Struct

```js
{
    functions: Object<String, Function>,
    dependencies: Option<DependencyConfig>,
    transformers: Option<Vec<Transformer | TransformerConfig>>,
}
```



### State

#### `<Zomelet>.functions`
Map of functions defined in this instance.


#### `<Zomelet>.zomes`
Map of expected zome interfaces.  Where the key is the zome name and the value is a `Zomelet`
definition.


#### `<Zomelet>.cells`
Map of expected cell interfaces.  Where the key is the role name and the value is a
[`CellZomelets`](CellZomelets.md) definition.


#### `<Zomelet>.virtual_cells`
Map of expected virtual cell interfaces.  Where the key is the role name and the value is a
[`CellZomelets`](CellZomelets.md) definition.


### Transformers

#### `<Zomelet>.processInput( input )`
Run all input transformers in the reverse order they were defined.


#### `<Zomelet>.processOutput( output )`
Run all output transformers in the order they were defined.



## Functions
A map of functions


### Function Types
There are 2 types of Zomelet functions and 4 ways to define them.

1. Real functions
   - `async function` - customized handling of input/output for `this.call(...)`
   - `true` - (pass-through) input/output are unchanged
   - `string` - (alias) call is forwarded to another function
2. Virtual functions
   - `async function` - handling that does not call `this.call(...)`

> **NOTE:** All zomelet functions are async.  Even when a function is not defined as async, all
> responses will result in Promises due to async input/output filtering support.


### Function Scope (`this`)

- Functions are passed a [CallContext](CallContext.md) object as `this` and the function input as
  `arguments[0]`
- The [CallContext](CallContext.md) scope is specific to each time the Zomelet function is called
  *(ie. every invocation of a Zomelet function creates a `new CallContext(...)`)*
- `this.call(...)` will proceed with a client call to the corresponding zome function


### Define a Zome Function
A basic function handler that pipes the output into a `Thing` class.

```js
{
    async get_thing ( id ) {
        return new Thing( await this.call( id ) );
    },
}
```

### Enable a Zome Function

```js
{
    "get_thing": true,
}
```

shorthand for

```js
{
    async get_thing ( ...args ) {
        return await this.call( ...args );
    },
}
```


### Define an alias

```js
{
    "alias_for_get_thing": "get_thing",
}
```

shorthand for

```js
{
    async alias_for_get_thing ( ...args ) {
        return await this.functions.get_thing( ...args );
    },
}
```

### Define a Virtual Function
An example of using a virtual function to check things and conditionally call another function.

```js
{
    create_memory: true,
    memory_exists: true,

    // Virtual function (ie. does not use `this.call(...)`
    async create_memory_if_not_exists ( bytes ) {
        const addr = await this.functions.memory_exists( bytes );

        if ( addr )
            return addr;

        return await this.functions.create_memory( bytes );
    },
}
```



## Dependencies
A Zomelet can depend on other Zomelets.


### `DependencyConfig` Struct
```js
{
    zomes: Option<Object<String, Zomelet | ZomeletConfig>>,
    cells: Option<Object<String, CellZomelets | CellZomeletsConfig>>,
    virtual: Option<{
        cells: Object<String, CellZomelets | CellZomeletsConfig>,
    }>,
}
```


### Peer Zomes
When the expected Zomelet interface belongs to a peer zome

```js
{
    zomes: {
        "other_zome": OtherZomelet,
    },
}
```

This interface would then be avaiable in the `CallContext` under `this.zomes`
```js
{
    async call_other_zome () {
        return await this.zomes.other_zome.some_function();
    },
}
```


### Peer Cells
When the expected Zomelet interface(s) belong to a peer cell

```js
{
    cells: {
        "other_role": OtherCellZomelets,
    },
}
```

This interface would then be avaiable in the `CallContext` under `this.cells`
```js
{
    async call_other_cell () {
        return await this.cells.other_role.some_zome.some_function();
    },
}
```


### Virtual Cells
When the expected Zomelet interface(s) do not map to a real cell, the client must have a defined
virtual cell to handle the interface calls.

```js
{
    virtual: {
        cells: {
            "other_virtual": OtherCellZomelets,
        },
    },
}
```

This interface would then be avaiable in the `CallContext` under `this.virtual.cells`
```js
{
    async call_virtual_cell ( dna_hash ) {
        return await this.virtual.cells.other_role( dna_hash ).some_zome.some_function();
    },
}
```

Since virtual cells do not map to a real cell, there is no DNA hash associated with the virtual
interface.  The interface must first be scoped to a DNA before the functions can be called.

#### What happens when the Zomelet with a cell dependency belongs to a virtual cell?
If this Zomelet was loaded as a virtual interface, we must also assume its dependencies are virtual.
Cell dependencies automatically become virtual cell dependencies if the dependent Zomelet is used by
a virtual cell.



## Transformers
See [Transformer.md](Transformer.md)


### `input` Structure

```js
[
    func : String,
    args : Option<Any>,
    options : Option<Number | Object>,
]
```

Example
```js
{
    async input ([ func, args, options ]) {
        return [ func, args, options ];
    },
}
```
