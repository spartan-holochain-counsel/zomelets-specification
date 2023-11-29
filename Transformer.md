[Back to README.md](README.md)


# `Transformer` Instance
A transformer is a configuration that has input and/or output handlers.

### `constructor( TransformerConfig )`

#### `TransformerConfig` Struct

```js
{
    input: Option<AsyncFunction>,
    output: Option<AsyncFunction>,
}
```

Example
```js
new Transformer({
    async input ( input ) {
        // modify input or return new input
    },
    async output ( output ) {
        // modify output or return new output
    }
})
```

The `input` structure will depend on where the transformer is applied (eg. Zomelet, Cell Zomelets).
`output` structure is implementation specific.


#### `<Transformer>.has_input_transformer -> Boolean`
True if this instance has a defined input handler.


#### `<Transformer>.has_output_transformer -> Boolean`
True if this instance has a defined output handler.


#### `<Transformer>.transformInput( input )`
Process data using input transformer.


#### `<Transformer>.transformOutput( output )`
Process data using output transformer.
