---
title: Lenses
category: how-to
menu_order: 3
---

# Lenses

Myriad can also generate [lenses](https://fsprojects.github.io/FSharpPlus/tutorial.html#Lens) for records and single-case discriminated unions.
Lens is a pair of a getter and a setter for one property of the type and allows, given the object, to get the value of the property or to update it creating a new object. The advantage of lenses is an ability to combine them to read or update nested fields of the object.

To create lenses for your type, first annotate the type for which you want lenses to be generated with `Generator.Lenses` attribute:

```fsharp
[<Generator.Lenses>]
type Record =
    { one: int
      two: string }
```

Myriad will generate the following code:

```fsharp
module RecordLenses =
    let one = (fun (x: Test1) -> x.one), (fun (x: Test1) (value: int) -> { x with one = value })
    let two = (fun (x: Test1) -> x.two), (fun (x: Test1) (value: string) -> { x with two = value })
```

Often lenses are defined as a single-case union around a pair of getter and setter. Myriad is also capable of adding the invocation of such DU's constructor.

To achieve this, decorate your type with the `Lens` attribute, specifying the name of the DU constructor: `[<Generator.Lenses("Lens")>]`, and Myriad will generate this code:

```fsharp
module RecordLenses =
    let one = Lens((fun (x: Test1) -> x.one), (fun (x: Test1) (value: int) -> { x with one = value }))
    let two = Lens((fun (x: Test1) -> x.two), (fun (x: Test1) (value: string) -> { x with two = value }))
```

You can provide the name of DU constructor in several ways:
- As a string: `[<Generator.Lenses("Lens")>]`;
- Or as a type: `[<Generator.Lenses(typedefof<Lens<_, _>>)>]` or `[<Generator.Lenses(typeof<Lens<_, _>>)>]`.

If the `Lens` type is in different namespace/module than the type decorated with the attribute, provide the full name of the `Lens` constructor: `[<Generator.Lenses("Namespace.And.Module.Of.Lens")>]`.