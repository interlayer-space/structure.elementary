# Interlayer / Structure.Elementary

This project is dedicated to provide basic form of data representation
that may be used as a common ground for serialization, manipulation and 
mapping code. It is implied that most data can be represented using just
six types - null, boolean, number, text, group and key-value structure.

This project follows basic concepts of
[interlayer.space](https://interlayer-space.github.io) library 
development, including maturity stages.

## Repositories

- [C#](https://github.com/interlayer-space/structure.elementary-cs)
- [Java](https://github.com/interlayer-space/structure.elementary-java)

## Rationale

Currently most serialization and deserialization libraries provide 
following interface:

```
serialization format -> user-defined types (POCO, POJO, etc.) 
```

Sometimes there is intermediate representation that may or may not be 
exposed to user code:

```
serialization format -> IR -> user-defined types
```

however, even when exposed, such intermediate representation allows end
user only to manipulate it to return it back to the very same library,
being not interoperable.

This projects stands to provide commonly-recognized interoperable 
representation of data structures without being tied to particular 
libraries:

```
A.deserialzie(serialized) -> Structure.Elementary types
Middleware.process(Structure.Elementary types):
  B.stripSensitive(Structure.Elementary types) -> Structure.Elementary types
  C.addUniqueId(Structure.Elementary types) -> Structure.Elementary types
C.map(Structure.Elementary types) -> user-defined types
```

Such unification would bring following benefits: 

- Input and output of different serializers and deserializers would be 
  converted into common API. This would allow both seamless 
  serializer / deserializer plugging, as well as ability to do mapping
  into another format without even knowing which data is mapped and 
  without creating some intermediate types that would serve as a bridge
  between libraries A and B.
- A layer between serializers and mappers would be created. Most 
  serialization libraries do both - they not only encode and decode data 
  in some arbitrary format, but also reconstruct objects and structures 
  of specified type. They should not do both, instead, it would be 
  enough to have just one mapper for all possible formats and number of 
  serializers, so end user would not care about having ton of 
  annotations and/or slightly different types for different serializers.
- A unified interface for working with unstructured data would be 
  introduced. It's not always possible to use concrete type for data, 
  but that doesn't mean there would be no operations taken over it. For 
  example, structure passed to program is intended to be processed with 
  JSONPatch operations, or a special gateway may replace patterns 
  `SCP-\d+` with `████-███-██████`.

## What's inside?

This project is in active development stage, so things may change.
Following components may be present (or absent, being not yet developed)
in library for each language:

- Core: provides interfaces for six primitive types containers.
- Traversal: interfaces for traversing arbitrary structure, e.g. path
  to reach specific node or pattern that selects all nodes matching 
  specific path pattern.
- Translation: interfaces for converting particular implementation input
  and output into structures of intermediate types.
- Manipulation: basic interfaces for code that wants to change 
  structure, JSONPatch being an example of such (but obviously with use
  of this library it would be applicable to any format).
- Production: interfaces for creating and duplicating structures.

## Concepts

This project implies null absence, unless otherwise stated in particular
repository. That means that there could not be case when node encloses
null value, when one of node children is null or a null could be passed
in any method, implementations that do that are considered essentially
broken. Some methods _may_ return nulls when there is no `Optional`-like
structure in standard library for particular language, but such methods
must have appropriate comments.

This project is not targeting to be compatible with every serialization,
deserialization or mapping case. As for now, this project also doesn't
put performance as top priority, thus it prefers to provide interfaces
for complete data structures rather than primitives for async code or
some other functionality which certainly isn't used in majority of
applications. This may be addressed in future, but original goal of
providing user-friendly structure representation that is perceived as
complete (even if it's not fully read from somewhere yet) will always be
a priority.

Repositories, for languages that allow that, expose `NodeKind` enum.
This enum contains all six base types and a `Special` / `SPECIAL` kind
for all extra node types introduced by derivations, for example, common
`MissingNode` pattern used in many libraries. Consuming code should
account for such kind and pass it through where possible, falling back
to errors otherwise.

Where applicable, `.asX()` and `.toX()` methods are exposed. First 
method pattern follows C# `as` keyword behavior, trying to cast current
instance to X or returning empty response (null or empty Option-like 
value), while `.toX()` returns X instance or results in an error.

## Licensing

Every repository is dual-licensed using MIT and UPL-1.0 licenses.

Feel free to use in any way you like.
