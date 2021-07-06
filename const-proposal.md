# Proposal for const and first-class types

```
Primitive types

i{8..64, size}
u{8..64, size}

T === runtime version of T
const T === compile time version of T

type === compile time type
dyn type === run time type

char
str
&T
&raw T
[T; length] for some (T: type, length: const usize)
```

# Initialisation syntax

```
<VARIABLE_NAME> ":" <VARIABLE_TYPE>
<VARIABLE_NAME> ":" <VARIABLE_TYPE> "=" <VARIABLE_BODY>
<VARIABLE_NAME> ":" "=" <VARIABLE_BODY>
```

# Functions

```
"const"? "(" (<INITIALISATION> ",")* ")" "=>" <FUNCTION_RET_TYPE>? <BLOCK>
```
You can use initialisation syntax in function argument bodies, including default values.

## Structs and enums

There are three ways of creating values of type `type`.

One is aliasing:
```
IntArray := Array(int);
```

Another one is creating a struct:
```
Dog := struct(foo: int, bar: str);
```
You can use initialisation syntax within struct bodies, including default values.


Another one is creating an enum:
```
Color := enum(
    Red,
    Green,
    Blue(extra_info: str),
);
```
You can use initialisation syntax within enum variants, including default values.

You can also create functions that return types. This is how generics are done.
```
Array := (T: type) => struct(
    data: &raw T,
    length: usize,
    capacity: usize,
);
```

Notice that `Array(int)` is a different type than `Array(float)`, but all
`Array(int)` calls point to the same struct.

Hash has nominal typing, but "remembers" which function a given struct or enum
came from, so that successive invocations of a const function, with the same
parameters, will be strictly cached, and will return the same nominal type.


## Const functions

```
Vector1 := (T: type) => struct(data: &raw T, length: usize, capacity: usize);
Vector2 := (T: type, initial_length: usize) => struct(data: &raw T, length = initial_length, capacity: usize);
Vector3 := (T: type, initial_length: const usize) => struct(data: &raw T, length = initial_length, capacity: usize);

x: Vector1(int); // okay, Vector1 is const.
z: Vector2(int, 3); // not okay, Vector3 is not const because it takes a run-time length parameter.
w: Vector3(int, 3); // okay, Vector3 is const because it takes a compile-time length parameter.
```


