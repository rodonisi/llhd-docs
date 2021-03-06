---
title: Operations
nav_order: 3
---
{::options toc_levels="1..3" /}

# Operations
{: .no_toc}

1. toc
{:toc}

<!-- Autogenerated by mlir-tblgen; don't manually edit -->
### `llhd.and` (llhd::AndOp)

Bitwise AND

Takes two integers of the same width or two nine-valued-logic (IEEE 1164)
values of the same width as input. Calculates the bitwise AND. The
result is always of the exact same type as the two inputs.

**Syntax:**
```
and-op ::= ssa-id `=` `llhd.and` ssa-lhs `,` ssa-rhs attr-dict `:` type
```

**Examples:**
```
%0 = llhd.const 0 : i32
%1 = llhd.and %0, %0 : i32
```

**Truth Table for `iN`:**

| `and` |  0  |  1  |
|:-----:|:---:|:---:|
|   0   |  0  |  0  |
|   1   |  0  |  1  |

**Truth Table for `lN`:**

| `and` |  U  |  X  |  0  |  1  |  Z  |  W  |  L  |  H  |  -  |
|:-----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|   U   |  U  |  U  |  0  |  U  |  U  |  U  |  0  |  U  |  U  |
|   X   |  U  |  X  |  0  |  X  |  X  |  X  |  0  |  X  |  X  |
|   0   |  0  |  0  |  0  |  0  |  0  |  0  |  0  |  0  |  0  |
|   1   |  U  |  X  |  0  |  1  |  X  |  X  |  0  |  1  |  X  |
|   Z   |  U  |  X  |  0  |  X  |  X  |  X  |  0  |  X  |  X  |
|   W   |  U  |  X  |  0  |  X  |  X  |  X  |  0  |  X  |  X  |
|   L   |  0  |  0  |  0  |  0  |  0  |  0  |  0  |  0  |  0  |
|   H   |  U  |  X  |  0  |  1  |  X  |  X  |  0  |  1  |  X  |
|   -   |  U  |  X  |  0  |  X  |  X  |  X  |  0  |  X  |  X  |

#### Operands:

| Operand | Description |
| :-----: | ----------- |
`lhs` | signless integer
`rhs` | signless integer

#### Results:

| Result | Description |
| :----: | ----------- |
&laquo;unnamed&raquo; | signless integer

### `llhd.const` (llhd::ConstOp)

Introduce a new constant.

The `llhd.const` instruction introduces a new constant value as an
SSA-operator.
Legal types are integers and time. Note: Signals
are not legal to define using `llhd.const`, use the `llhd.sig`
instruction for that.

**Custom syntax:**

```
const-op ::= ssa-id `=` `llhd.const` attribute-value attr-dict `:` result-type
```

**Examples:**

```
%0 = llhd.const 1 : i64
%1 = llhd.const #llhd.time<1ns, 2d, 3d> : !llhd.time
```

#### Attributes:

| Attribute | MLIR Type | Description |
| :-------: | :-------: | ----------- |
`value` | Attribute | any attribute

#### Results:

| Result | Description |
| :----: | ----------- |
`out` | signless integer or LLHD time type

### `llhd.dexts` (llhd::DextsOp)

Dynamically extract a slice of consecutive elements

Syntax:

```
operation ::= `llhd.dexts` operands attr-dict `:` functional-type(operands, results)
```


The `llhd.dexts` operation allows to dynamically access a slice of the
`$target` operand, starting at the index given by the `$start` operand.
The resulting slice length is defined by the result type.
The `$target` operand kind has to match the result kind.
If `$target` is a vector, only the number of elements can change, while
the element type has to remain the same.

**Examples:**
```
%0 = llhd.const 0x0f0 : i12
%1 = llhd.const 4 : i3

%3 = llhd.dexts %0, %1 : (i12, i3) -> i4    // %3: 0xf
```

#### Operands:

| Operand | Description |
| :-----: | ----------- |
`target` | signless integer or vector of any type values or LLHD sig type of signless integer or LLHD time type values
`start` | signless integer

#### Results:

| Result | Description |
| :----: | ----------- |
`result` | signless integer or vector of any type values or LLHD sig type of signless integer or LLHD time type values

### `llhd.drv` (llhd::DrvOp)

Drive a value into a signal.

Syntax:

```
operation ::= `llhd.drv` $signal `,` $value `after` $time ( `if` $enable^ )? attr-dict `:` type($signal)
```


The `llhd.drv` operation drives a new value onto a signal. A time
operand also has to be passed, which specifies the frequency at which
the drive will be performed. An optional enable value can be passed as
last argument. In this case the drive will only be performed if the
value is 1. In case no enable signal is passed the drive will always be
performed. This operation does not define any new SSA operands.

**Custom Syntax:**

```
drv-op ::= `llhd.drv` ssa-signal `,` ssa-const `after` ssa-time (`if` ssa-enable)? `:` !llhd.sig<const-type>
```

**Examples:**

```
%init = llhd.const 1 : i1
%en = llhd.const 0 : i1
%time = llhd.const #llhd.time<1ns, 0d, 0e> : !llhd.time
%sig = llhd.sig %init : i1
%new = llhd.not %init : i1

llhd.drv %sig, %new after %time : !llhd.sig<i1>
llhd.drv %sig, %new after %time if %en : !llhd.sig<i1>
```

#### Operands:

| Operand | Description |
| :-----: | ----------- |
`signal` | LLHD sig type of signless integer or LLHD time type values
`value` | signless integer
`time` | LLHD time type
`enable` | 1-bit signless integer

### `llhd.entity` (llhd::EntityOp)

Create an entity.

The `llhd.entity` operation defines a new entity unit. An entity
represents the data-flow description of how a circuit's output values
change in reaction to changing input values.
An entity contains one region with a single block and an implicit
`TerminatorOp` terminator. Both the block name and terminator are
omitted in the custom syntax. No further blocks and control-flow are
legal inside an entity.

**Custom syntax:**
```
entity-op ::= `llhd.entity` entity-symbol `(` arg-list `)` `->` `(` out-list `)` attr-dict entity-region
```

**Examples:**
```
llhd.entity @Foo () -> () {
    %0 = llhd.const 0 : i1
    %toggle = llhd.sig %0 : i1 -> !llhd.sig<i1>
    %1 = llhd.prb %toggle : !llhd.sig<i1> -> i1
    %2 = llhd.not %1 : i1
    %dt = llhd.const #llhd.time<1ns, 0d, 0e> : !llhd.time
    llhd.drv %toggle, %2, %dt : !llhd.sig<i1>, i1, !llhd.time
}
```

#### Attributes:

| Attribute | MLIR Type | Description |
| :-------: | :-------: | ----------- |
`ins` | IntegerAttr | 64-bit signless integer attribute

### `llhd.exts` (llhd::ExtsOp)

Extract a slice of consecutive elements.

Syntax:

```
operation ::= `llhd.exts` $target `,` $start `,` $length  attr-dict `:`
              type($target) `to` type($result)
```


The `llhd.exts` operation allows access to a slice of the `$target`
operand. The `$start` attribute defines the index of the first element,
while the `$length` attribute defines the stride of the slice.
The return type is the same as `$target` but with width `$length`.
If `%target` is a signal, a new subsignal aliasing the slice will be
returned.

**Examples:**
```
%0 = llhd.const 123 : i32
%1 = llhd.exts %0, 0, 2 : i32 to i2

%2 = llhd.sig %0 : i32
%3 = llhd.exts %2, 0, 5 : !llhd.sig<i32> to !llhd.sig<i5>
```

#### Attributes:

| Attribute | MLIR Type | Description |
| :-------: | :-------: | ----------- |
`start` | IntegerAttr | index attribute
`length` | IntegerAttr | index attribute

#### Operands:

| Operand | Description |
| :-----: | ----------- |
`target` | signless integer or LLHD sig type of signless integer or LLHD time type values

#### Results:

| Result | Description |
| :----: | ----------- |
`result` | signless integer or LLHD sig type of signless integer or LLHD time type values

### `llhd.halt` (llhd::HaltOp)

Terminates execution of a process.

Syntax:

```
operation ::= `llhd.halt` attr-dict
```


The `halt` instruction terminates execution of a process. All processes
must halt eventually or consist of an infinite loop.
* This is a terminator instruction
* This instruction is only allowed in processes (`llhd.proc`).

**Syntax:**
```
halt-op ::= `llhd.halt`
```

**Examples:**
```
llhd.halt
```

### `llhd.insf` (llhd::InsfOp)

Insert an element into a vector or tuple.

Syntax:

```
operation ::= `llhd.insf` $target `,` $element `,` $index attr-dict `:`
              type($target) `,` type($element)
```


The `llhd.insf` operation allows insertion of an element represented by
the `$element` operand into the `$target` operand. The `$index`
attribute defines the index where to insert the element. The return type
is the same as `$target`. Note that the `$target` is not changed, but a
new value with the element inserted is returned.

**Examples:**

```mlir
%target = constant dense<[1,2,3]> : vector<3xi8>
%element = llhd.const 2 : i8
%0 = llhd.insf %target, %element, 0 : vector<3xi8>, i8

%tuptarget = llhd.tuple %element, %target : tuple<i8, vector<3xi8>
%newelement = llhd.const 4 : i8
%1 = llhd.insf %tuptarget, %newelement, 0 : tuple<i8, vector<3xi8>>, i8
```

#### Attributes:

| Attribute | MLIR Type | Description |
| :-------: | :-------: | ----------- |
`index` | IntegerAttr | index attribute

#### Operands:

| Operand | Description |
| :-----: | ----------- |
`target` | vector of any type values or tuple
`element` | any type

#### Results:

| Result | Description |
| :----: | ----------- |
`result` | vector of any type values or tuple

### `llhd.inss` (llhd::InssOp)

Insert a slice of consecutive elements.

Syntax:

```
operation ::= `llhd.inss` $target `,` $slice `,` $start attr-dict `:`
              type($target) `,` type($slice)
```


The `llhd.inss` operation allows insertion of a slice represented by the
`$slice` operand into the `$target` operand. The `$start` attribute
defines the index of the first element. The return type is the same as
`$target`. Note that the `$target` is not changed, but a new value with
the slice inserted is returned.

**Examples:**

```mlir
%itarget = llhd.const 123 : i32
%islice = llhd.const 2 : i2
%0 = llhd.inss %itarget, %islice, 0 : i32, i2

%vtarget = constant dense<[1,2,3]> : vector<3xi32>
%vslice = constant dense<[4,5]> : vector<2xi32>
%1 = llhd.inss %vtarget, %vslice, 0 : vector<3xi32>, vector<2xi32>
```

#### Attributes:

| Attribute | MLIR Type | Description |
| :-------: | :-------: | ----------- |
`start` | IntegerAttr | index attribute

#### Operands:

| Operand | Description |
| :-----: | ----------- |
`target` | signless integer or vector of any type values
`slice` | signless integer or vector of any type values

#### Results:

| Result | Description |
| :----: | ----------- |
`result` | signless integer or vector of any type values

### `llhd.inst` (llhd::InstOp)

Instantiates a process or entity.

Syntax:

```
operation ::= `llhd.inst` $name $callee `(` $inputs `)` `->` `(` $outputs `)` attr-dict `:`
              functional-type($inputs, $outputs)
```


Instantiates a process or entity and thus allows to build hierarchies.
Can only be used within an entity. An instance defines a unique name
within the entity it resides in.

Syntax:
```
inst-op ::= `llhd.inst` inst-name symbol-name `(` ssa-input-list `)` `->` `(` ssa-output-list `)` attr-dict `:` functional-type(ssa-input-list, ssa-output-list)
```

Examples:
```
llhd.inst "foo" @empty() -> () : () -> ()
llhd.inst "bar" @proc_symbol() -> (%out0) : () -> !llhd.sig<i32>
llhd.inst "baz" @entity_symbol(%in0, %in1) -> (%out0, %out1) : (!llhd.sig<i32>, !llhd.sig<i16>) -> (!llhd.sig<i8>, !llhd.sig<i4>)
```

#### Attributes:

| Attribute | MLIR Type | Description |
| :-------: | :-------: | ----------- |
`name` | StringAttr | string attribute
`callee` | FlatSymbolRefAttr | flat symbol reference attribute

#### Operands:

| Operand | Description |
| :-----: | ----------- |
`inputs` | LLHD sig type of signless integer or LLHD time type values
`outputs` | LLHD sig type of signless integer or LLHD time type values

### `llhd.neg` (llhd::NegOp)

Negate a value.

The operand and result always have the same type. The type has to be a
signless integer of any width. Although, only signless integers are
allowed, this instruction applies two's complement negation of the
integer, basically treating it as a signed integer.

**Syntax:**
```
neg-op ::= ssa-id `=` `llhd.neg` ssa-value attr-dict `:` type
```

**Examples:**
```
%0 = llhd.const 42 : i32
%1 = llhd.neg %0 : i32
```

#### Operands:

| Operand | Description |
| :-----: | ----------- |
`value` | signless integer

#### Results:

| Result | Description |
| :----: | ----------- |
&laquo;unnamed&raquo; | signless integer

### `llhd.not` (llhd::NotOp)

Bitwise NOT

Takes an integer of any width or a nine-valued-logic (IEEE 1164) value
of any width as input. Flips each bit of a value. The result always has
the exact same type.

**Syntax:**
```
not-op ::= ssa-id `=` `llhd.not` ssa-value attr-dict `:` type
```

**Examples:**
```
%0 = llhd.const 0 : i32
%1 = llhd.not %0 : i32
```

**Truth Table for `iN`:**

| `not` |  0  |  1  |
|:-----:|:---:|:---:|
|       |  1  |  0  |

**Truth Table for `lN`:**

| `not` |  U  |  X  |  0  |  1  |  Z  |  W  |  L  |  H  |  -  |
|:-----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|       |  U  |  X  |  1  |  0  |  X  |  X  |  1  |  0  |  X  |

#### Operands:

| Operand | Description |
| :-----: | ----------- |
`value` | signless integer

#### Results:

| Result | Description |
| :----: | ----------- |
&laquo;unnamed&raquo; | signless integer

### `llhd.or` (llhd::OrOp)

Bitwise OR

Takes two integers of the same width or two nine-valued-logic (IEEE 1164)
values of the same width as input. Calculates the bitwise OR. The
result is always of the exact same type as the two inputs.

**Syntax:**
```
or-op ::= ssa-id `=` `llhd.or` ssa-lhs `,` ssa-rhs attr-dict `:` type
```

**Examples:**
```
%0 = llhd.const 0 : i32
%1 = llhd.or %0, %0 : i32
```

**Truth Table for `iN`:**

| `or`  |  0  |  1  |
|:-----:|:---:|:---:|
|   0   |  0  |  1  |
|   1   |  1  |  1  |

**Truth Table for `lN`:**

| `or`  |  U  |  X  |  0  |  1  |  Z  |  W  |  L  |  H  |  -  |
|:-----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|   U   |  U  |  U  |  U  |  1  |  U  |  U  |  U  |  1  |  U  |
|   X   |  U  |  X  |  X  |  1  |  X  |  X  |  X  |  1  |  X  |
|   0   |  U  |  X  |  0  |  1  |  X  |  X  |  0  |  1  |  X  |
|   1   |  1  |  1  |  1  |  1  |  1  |  1  |  1  |  1  |  1  |
|   Z   |  U  |  X  |  X  |  1  |  X  |  X  |  X  |  1  |  X  |
|   W   |  U  |  X  |  X  |  1  |  X  |  X  |  X  |  1  |  X  |
|   L   |  U  |  X  |  0  |  1  |  X  |  X  |  0  |  1  |  X  |
|   H   |  1  |  1  |  1  |  1  |  1  |  1  |  1  |  1  |  1  |
|   -   |  U  |  X  |  X  |  1  |  X  |  X  |  X  |  1  |  X  |

#### Operands:

| Operand | Description |
| :-----: | ----------- |
`lhs` | signless integer
`rhs` | signless integer

#### Results:

| Result | Description |
| :----: | ----------- |
&laquo;unnamed&raquo; | signless integer

### `llhd.prb` (llhd::PrbOp)

Probe a signal.

Syntax:

```
operation ::= `llhd.prb` $signal attr-dict `:` type($signal)
```


The `llhd.prb` instruction probes a signal and returns the value it
currently carries as a new SSA operand. The result type is always
the type carried by the signal.

 **Custom syntax:**

 ```
 prb-op ::= ssa-id `=` `llhd.prb` ssa-sig attr-dict `:` !llhd.sig<type>
 ```

 **Examples:***

 ```
 %const_i1 = llhd.const 1 : i1
 %sig_i1 = llhd.sig %const_i1 : i1
 %prbd = llhd.prb %sig_i1 : !llhd.sig<i1>
 ```

#### Operands:

| Operand | Description |
| :-----: | ----------- |
`signal` | LLHD sig type of signless integer or LLHD time type values

#### Results:

| Result | Description |
| :----: | ----------- |
`result` | signless integer

### `llhd.proc` (llhd::ProcOp)

Create a process

A `llhd.proc` represents control-flow in a timed fashion. It allows a
procedural description of how a circuit's output signals change in
reaction to changing input signals. It has a region with arbitrarily
many basic blocks. The first block is the entry block and cannot be
targeted by the terminators. It uses `llhd.wait` as a terminator to add
timed control-flow. Immediate control-flow with `br` or `cond_br` is
also possible. Every process must either contain an infinite loop or
terminate with the `llhd.halt` terminator.

How does a process compare to functions and entities?

| Unit     | Paradigm     | Timing    | Models                         |
|----------|--------------|-----------|--------------------------------|
| Function | control-flow | immediate | Computation in zero time       |
| Process  | control-flow | timed     | Behavioral circuit description |
| Entity   | data-flow    | timed     | Structural circuit description |

Syntax:
```
proc-op ::= `llhd.proc` proc-symbol `(` ssa-input-list `)` `->` `(` ssa-output-list `)` attr-dict `{` proc-region `}`
```

Examples:
```
llhd.proc @example(%in0 : !llhd.sig<i64>, %in1 : !llhd.sig<i1>) -> (%out2 : !llhd.sig<i1>) {
    br ^bb1
^bb1:
    llhd.halt
}
```

#### Attributes:

| Attribute | MLIR Type | Description |
| :-------: | :-------: | ----------- |
`ins` | IntegerAttr | 64-bit signless integer attribute

### `llhd.reg` (llhd::RegOp)

Represents a storage element

This instruction represents a storage element. It drives its output onto
the 'signal' value. An arbitrary amount of triggers can be added to the
storage element. However, at least one is required. They are quadruples
consisting of the new value to be stored if the trigger applies, the
mode and trigger value which specify when this trigger has to be applied
as well as a delay. Optionally, each triple may also have a gate
condition, in this case the trigger only applies if the gate is one. If
multiple triggers apply the left-most in the list takes precedence.

There are five modes available:

| Mode   | Meaning                                                     |
|--------|-------------------------------------------------------------|
| "low"  | Storage element stores `value` while the `trigger` is low. Models active-low resets and low-transparent latches. |
| "high" | Storage element stores `value` while the `trigger` is high. Models active-high resets and high-transparent latches. |
| "rise" | Storage element stores `value` upon the rising edge of the `trigger`. Models rising-edge flip-flops.
| "fall" | Storage element stores `value` upon the falling edge of the `trigger`. Models falling-edge flip-flops.
| "both" | Storage element stores `value` upon the a rising or a falling edge of the `trigger`. Models dual-edge flip-flops.

This instruction may only be used in an LLHD entity.

Syntax:

```
reg-op ::= `llhd.reg` signal-ssa-value ( `,` `(` value-ssa-value `,` mode-string trigger-ssa-value `after` delay-ssa-value ( `if` gate-ssa-value )? `:` value-type )+ attr-dict `:` signal-type
```

Examples:

A rising, falling, and dual-edge triggered flip-flop:

```
llhd.reg %Q, (%D, "rise" %CLK after %T : !llhd.sig<i8>) : !llhd.sig<i8>
llhd.reg %Q, (%D, "fall" %CLK after %T : !llhd.sig<i8>) : !llhd.sig<i8>
llhd.reg %Q, (%D, "both" %CLK after %T : !llhd.sig<i8>) : !llhd.sig<i8>
```

A rising-edge triggered flip-flop with active-low reset:

```
llhd.reg %Q, (%init, "low" %RSTB after %T : !llhd.sig<i8>), (%D, "rise" %CLK after %T : !llhd.sig<i8>) : !llhd.sig<i8>
```

A rising-edge triggered enable flip-flop with active-low reset:

```
llhd.reg %Q, (%init, "low" %RSTB after %T : !llhd.sig<i8>), (%D, "rise" %CLK after %T if %EN : !llhd.sig<i8>) : !llhd.sig<i8>
```

A transparent-low and transparent-high latch:

```
llhd.reg %Q, (%D, "low" %CLK after %T : !llhd.sig<i8>) : !llhd.sig<i8>
llhd.reg %Q, (%D, "high" %CLK after %T : !llhd.sig<i8>) : !llhd.sig<i8>
```

An SR latch:

```
%0 = llhd.const 0 : i1
%1 = llhd.const 1 : i1
llhd.reg %Q, (%0, "high" %R after %T : !llhd.sig<i1>), (%1, "high" %S after %T : !llhd.sig<i1>) : !llhd.sig<i1>
```

#### Attributes:

| Attribute | MLIR Type | Description |
| :-------: | :-------: | ----------- |
`modes` | ArrayAttr | reg mode array attribute
`gateMask` | ArrayAttr | 64-bit integer array attribute

#### Operands:

| Operand | Description |
| :-----: | ----------- |
`signal` | LLHD sig type of signless integer or LLHD time type values
`values` | signless integer or LLHD time type or LLHD sig type of signless integer or LLHD time type values
`triggers` | 1-bit signless integer
`delays` | LLHD time type
`gates` | 1-bit signless integer

### `llhd.smod` (llhd::SModOp)

Signed modulo.

This instruction computes the signed modulo of two signless integers of
any width, treating the leading bit as sign. The operand and result
types always have to be the same.
To calculate the signed remainder of two integers, use `remi_signed`
from the standard dialect.

**Syntax:**
```
smod-op ::= ssa-id `=` `llhd.smod` ssa-lhs `,` ssa-rhs attr-dict `:` type
```

**Examples:**
```
%0 = llhd.const 9 : i4
%1 = llhd.const 4 : i4
%2 = llhd.smod %0, %1 : i4
```

#### Operands:

| Operand | Description |
| :-----: | ----------- |
`lhs` | signless integer
`rhs` | signless integer

#### Results:

| Result | Description |
| :----: | ----------- |
&laquo;unnamed&raquo; | signless integer

### `llhd.shl` (llhd::ShlOp)

Shifts a value to the left by a given amount.

Syntax:

```
operation ::= `llhd.shl` operands attr-dict `:` functional-type(operands, results)
```


The type of the base value and the hidden value must be the same, but
may differ in the number of bits or elements. The result always has the
same type (including width) of the base value.
The instruction is transparent to signals and pointers. For example,
passing a signal as argument will shift the underlying value and return
a signal to the shifted value.
Allowed (underlying) types are signless integers, nine-valued-logic values
and arrays. The shift amount has to be a signless integer. A shift amount
bigger than the number of bits or elements of the hidden value is undefined.
The hidden value is uncovered by non-zero shift amounts. E.g. consider
the four bit values `base = 0xf`, `hidden = 0xc` shifted by an amount of
three result in `0xe`.

**Syntax:**
```
shl-op ::= ssa-id `=` `llhd.shl` ssa-base `,` ssa-hidden `,` ssa-amount attr-dict `:` `(` base-type `,` hidden-type `,` amount-type `)` `->` result-type
```

**Examples:**
```
%0 = llhd.shl %base, %hidden, %amount : (i4, i2, i2) -> i4
```

#### Operands:

| Operand | Description |
| :-----: | ----------- |
`base` | signless integer or LLHD sig type of signless integer or LLHD time type values
`hidden` | signless integer or LLHD sig type of signless integer or LLHD time type values
`amount` | signless integer

#### Results:

| Result | Description |
| :----: | ----------- |
`result` | signless integer or LLHD sig type of signless integer or LLHD time type values

### `llhd.shr` (llhd::ShrOp)

Shifts a value to the right by a given amount.

Syntax:

```
operation ::= `llhd.shr` operands attr-dict `:` functional-type(operands, results)
```


The type of the base value and the hidden value must be the same, but
may differ in the number of bits or elements. The result always has the
same type (including width) of the base value.
The instruction is transparent to signals and pointers. For example,
passing a signal as argument will shift the underlying value and return
a signal to the shifted value.
Allowed (underlying) types are signless integers, nine-valued-logic values
and arrays. The shift amount has to be a signless integer. A shift amount
bigger than the number of bits or elements of the hidden value is undefined.
The hidden value is uncovered by non-zero shift amounts. E.g. consider
the four bit values `base = 0xf`, `hidden = 0xc` shifted by an amount of
three result in `0x9`.

**Syntax:**
```
shr-op ::= ssa-id `=` `llhd.shr` ssa-base `,` ssa-hidden `,` ssa-amount attr-dict `:` `(` base-type `,` hidden-type `,` amount-type `)` `->` result-type
```

**Examples:**
```
%0 = llhd.shr %base, %hidden, %amount : (i4, i2, i2) -> i4
```

#### Operands:

| Operand | Description |
| :-----: | ----------- |
`base` | signless integer or LLHD sig type of signless integer or LLHD time type values
`hidden` | signless integer or LLHD sig type of signless integer or LLHD time type values
`amount` | signless integer

#### Results:

| Result | Description |
| :----: | ----------- |
`result` | signless integer or LLHD sig type of signless integer or LLHD time type values

### `llhd.sig` (llhd::SigOp)

Create a signal.

Syntax:

```
operation ::= `llhd.sig` $name $init attr-dict `:` type($init)
```


The `llhd.sig` instruction introduces a new signal in the IR. The input
operand determines the initial value carried by the signal, while the
result type will always be a signal carrying the type of the init operand.
A signal defines a unique name within the entity it resides in. Signals
can only be allocated within entities.

**Custom syntax:**

```
sig-op ::= ssa-id `=` `llhd.sig` sig-name ssa-init attr-dict `:` init-type
```

**Examples:**

```
%init_i64 = llhd.const 123 : i64
%sig_i64 = llhd.sig "foo" %init_64 : i64

%init_i1 = llhd.const 1 : i1
%sig_i1 = llhd.sig "bar" %init_i1 : i1
```
The first `llhd.sig` instruction creates a new signal named "foo", carrying an `i64`
type with initial value of 123, while the second one creates a new signal
named "bar", carrying an `i1` type with initial value of 1.

#### Attributes:

| Attribute | MLIR Type | Description |
| :-------: | :-------: | ----------- |
`name` | StringAttr | string attribute

#### Operands:

| Operand | Description |
| :-----: | ----------- |
`init` | signless integer

#### Results:

| Result | Description |
| :----: | ----------- |
`result` | LLHD sig type of signless integer or LLHD time type values

### `llhd.terminator` (llhd::TerminatorOp)

Dummy terminator

The `"llhd.terminator"` op is a dummy terminator for an `EntityOp` unit.
It provides no further meaning other than ensuring correct termination
of an entitiy's region. This operation provides no custom syntax and
should never explicitly appear in LLHD's custom syntax.

### `llhd.tuple` (llhd::TupleOp)

Create a tuple from a list of values.

Syntax:

```
operation ::= `llhd.tuple` $values attr-dict `:` type($result)
```


The `llhd.tuple` operation creates a tuple from a list of SSA-values.

**Examples:**

```
%c1 = llhd.const 1 : i32
%c2 = llhd.const 2 : i2
%sig = llhd.sig "sig_name" %c1 : i32
%vec = constant dense<[1, 2]> : vector<2xi32>
%tuple = llhd.tuple %c1, %c2, %vec, %sig : tuple<i32, i2, vector<2xi32>, !llhd.sig<i32>>
```

#### Operands:

| Operand | Description |
| :-----: | ----------- |
`values` | any type

#### Results:

| Result | Description |
| :----: | ----------- |
`result` | tuple

### `llhd.vec` (llhd::VecOp)

Create a vector from a list of values.

Syntax:

```
operation ::= `llhd.vec` $values attr-dict `:` type($result)
```


The `llhd.vec` operation allows to create a vector from a list of
SSA-values. This allows for more flexibility compared to only using
`std.constant` and the vector dialect operations.

**Examples:**

```
%c1 = llhd.const 1 : i32
%c2 = llhd.const 2 : i32
%c3 = llhd.const 3 : i32
%vec = llhd.vec %c1, %c2, %c3 : vector<3xi32>
%elem = vector.extractelement %vec[%c1 : i32] : vector<3xi32>
```

#### Operands:

| Operand | Description |
| :-----: | ----------- |
`values` | any type

#### Results:

| Result | Description |
| :----: | ----------- |
`result` | vector of any type values

### `llhd.wait` (llhd::WaitOp)

Suspends execution of a process.

Syntax:

```
operation ::= `llhd.wait` (`for` $time^ `,`)? (`(`$obs^ `:` type($obs)`)` `,`)?
              $dest (`(` $destOps^ `:` type($destOps) `)`)? attr-dict
```


The `wait` instruction suspends execution of a process until any of the
observed signals change or a fixed time interval has passed. Execution
resumes at the specified basic block with the passed arguments.
* This is a terminator instruction.
* This instruction is only allowed in processes (`llhd.proc`).

**Syntax:**
```
wait-op ::= `llhd.wait` ( `for` ssa-time `,` )? ( `(` ssa-list-obs `:` type-list-obs `)` `,` )? successor-dest ( `(` ssa-list-dest-arguments `:` type-list-dest-arguments `)` )? attr-dict
```
Notes:
* `ssa-list-obs`, `ssa-list-dest-arguments`, `type-list-dest-arguments` and `type-list-obs` are comma-separated lists of 0 or more elements.

**Examples:**
```
llhd.wait ^bb1
llhd.wait for %time, ^bb1(%time : !llhd.time)
llhd.wait (%0, %1 : !llhd.sig<i64>, !llhd.sig<i1>), ^bb1(%1 : !llhd.sig<i1>)
llhd.wait for %time, (%0, %1 : !llhd.sig<i64>, !llhd.sig<i1>), ^bb1(%1, %0 : !llhd.sig<i1>, !llhd.sig<i64>)
```

#### Operands:

| Operand | Description |
| :-----: | ----------- |
`obs` | LLHD sig type of signless integer or LLHD time type values
`time` | LLHD time type
`destOps` | any type

#### Successors:

| Successor | Description |
| :-------: | ----------- |
`dest` | any successor

### `llhd.xor` (llhd::XorOp)

Bitwise XOR

Takes two integers of the same width or two nine-valued-logic (IEEE 1164)
values of the same width as input. Calculates the bitwise XOR. The
result is always of the exact same type as the two inputs.

**Syntax:**
```
xor-op ::= ssa-id `=` `llhd.xor` ssa-lhs `,` ssa-rhs attr-dict `:` type
```

**Examples:**
```
%0 = llhd.const 0 : i32
%1 = llhd.xor %0, %0 : i32
```

**Truth Table for `iN`:**

| `xor` |  0  |  1  |
|:-----:|:---:|:---:|
|   0   |  0  |  1  |
|   1   |  1  |  0  |

**Truth Table for `lN`:**

| `xor` |  U  |  X  |  0  |  1  |  Z  |  W  |  L  |  H  |  -  |
|:-----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|   U   |  U  |  U  |  U  |  U  |  U  |  U  |  U  |  U  |  U  |
|   X   |  U  |  X  |  X  |  X  |  X  |  X  |  X  |  X  |  X  |
|   0   |  U  |  X  |  0  |  1  |  X  |  X  |  0  |  1  |  X  |
|   1   |  U  |  X  |  1  |  0  |  X  |  X  |  1  |  0  |  X  |
|   Z   |  U  |  X  |  X  |  X  |  X  |  X  |  X  |  X  |  X  |
|   W   |  U  |  X  |  X  |  X  |  X  |  X  |  X  |  X  |  X  |
|   L   |  U  |  X  |  0  |  1  |  X  |  X  |  0  |  1  |  X  |
|   H   |  U  |  X  |  1  |  0  |  X  |  X  |  1  |  0  |  X  |
|   -   |  U  |  X  |  X  |  X  |  X  |  X  |  X  |  X  |  X  |

#### Operands:

| Operand | Description |
| :-----: | ----------- |
`lhs` | signless integer
`rhs` | signless integer

#### Results:

| Result | Description |
| :----: | ----------- |
&laquo;unnamed&raquo; | signless integer

