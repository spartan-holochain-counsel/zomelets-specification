[Back to README.md](README.md)


# `CallContext` Instance

#### `<CallContext>.name`
A name to distinguish one call context from another.

Eg. `<role name>::<zome name>-><func name> [<duration>]`


## Input Context

#### `<CallContext>.cell`
Instance of `ScopedCellZomelets` that created `this.zome`


#### `<CallContext>.zome`
Instance of `ScopedZomelet` that created this `CallContext`


#### `<CallContext>.func`
Name of the Zomelet function that created this `CallContext`


#### `<CallContext>.args`
The arguments that were passed in for this call


#### `<CallContext>.call_options`
The options that were passed in for this call


## Relative Context

#### `<CallContext>.heritage`
Chain of parents that lead to this instance.


#### `<CallContext>.parent`
Short-hand for last item in heritage.  Or `null` if heritage is empty.


#### `<CallContext>.children`
List of subcalls created by this instance.


#### `<CallContext>.position`
The index of this subcall in its parent's children list.  Defaults to `0`


#### `<CallContext>.depth`
The length of this instance's heritage.  Defaults to `0`


## State Info

#### `<CallContext>.start_time`
Timestamp of when this instance was created.

#### `<CallContext>.end_time`
Timestamp of when `this.end()` was called.


#### `<CallContext>.duration`
The difference between `this.start_time` and `this.end_time` (or `Date.now()` if end time is not
set)


#### `<CallContext>.tree`
An array of strings that illustrate the linear progression of the call and subcalls.


#### `<CallContext>.subcallCount()`
Number of subcalls including all level of decedents.


## Interface Context

#### `<CallContext>.cells`
Map of available peer cell interfaces.

#### `<CallContext>.zomes`
Map of available peer zome interfaces.


#### `<CallContext>.functions`
Map of other functions in this interface.


#### `<CallContext>.virtual`
Map of virtual peer cell interfaces.


## Methods

#### `<CallContext>.call( input, options )`
Call the real zome function that this context is scoped to.


#### `<CallContext>.cancel()`
Call `end()` on this instance if it is not already ended.


#### `<CallContext>.end()`
Mark this instance as ended and cancel any unfinished subcalls.


#### `<CallContext>.childContext( scoped_zome, fn_name, args, options )`
Create a subcall context under this one.  `options` will inherit values from the `call_options` in
this instance.


#### `<CallContext>.getCellInterface( role, dna )`
Setup a virtual cell interface for the given DNA or, if the DNA matches, use the real cell
interface.

When a real and virtual cell have overlapping names, this method will check if the real cell DNA
matches the given DNA
