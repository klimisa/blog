---
layout: post
title: Port Cargo Tracker to FSharp
---

The first thing I strungled was name of the project and the namespace to use for the new fsharp implementation of the domain.
I started with the easiest class which was the `TrackingId`.

What work was just the namespace `FSharp.Domain.Shipping.Cargo` and for project name `Domain.FSharp`
The end result is the same as when you write C#.

> Note: As with all user-defined data types in F#, you declare classes with the type keyword. (Rather than requiring different keywords for every data type you can create, the compiler infers the construct based on its structure.)

```csharp
// Decompiled with JetBrains decompiler
// Type: FS.Domain.Shipping.Cargo.TrackingId
// Assembly: Domain.FSharp, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
// MVID: DD3B022D-0E5C-FFE6-27C0-645C9B4C415D
// Assembly location: C:\akli\dev\CargoTracker2FSharp\Domain.FSharp\bin\Debug\netstandard2.0\Domain.FSharp.dll

using Microsoft.FSharp.Core;
using System;

namespace FSharp.Domain.Shipping.Cargo
{
  [CompilationMapping(SourceConstructFlags.ObjectType)]
  [Serializable]
  public class TrackingId
  {
    internal Guid value;

    public TrackingId(Guid value)
    {
      TrackingId trackingId = this;
      this.value = value;
    }

    public Guid Value
    {
      get
      {
        return this.value;
      }
    }
  }
}
```

In any other combination the result are different with the compiler to give me a `static` class.

Next thing I picked up was the enumerator `Handling Type`

```csharp
public enum HandlingType
  {
    Load,
    Unload,
    Receive,
    Claim,
    Customs,
  }
```

in order to have the same result an emun in C# I used `Enumerations` from F#

```fsharp
type enum-name =
| value1 = integer-literal1
| value2 = integer-literal2
...
```

```fsharp
type HandlingType =
    | Load = 0
    | Unload = 1
    | Receive = 2
    | Claim = 3
    | Customs = 4
```

Next was `Unlocode` which had validation of constructor agruments.

Anything you need to do for validation you can use the do binding.

```fsharp
type UnLocode(value: string) =

    do
        if value = null then raise (ArgumentNullException("value"))

    do
        let pattern = Regex "[a-zA-Z]{2}[a-zA-Z2-9]{3}"
        let matches = pattern.Match value
        if not matches.Success then
          raise (ArgumentException("Provided value is not a valid UnLocode", "value"))

    member val Value = value
```

Another thing I want to mention is that if you use "self-identifier" `member this.Value = value` instead of an implicit property `member val Value = value`, the class field is instantiate before the "do bindings" on the generated code

- With self-identifier

```csharp
// Decompiled with JetBrains decompiler
// Type: FSharp.Domain.Shipping.Cargo.UnLocode
// Assembly: Domain.FSharp, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
// MVID: DCF9542A-4C7C-4DDD-AAA5-A718372534E8
// Assembly location: C:\akli\dev\CargoTracker2FSharp\Domain.FSharp\bin\Debug\netstandard2.0\Domain.FSharp.dll

using Microsoft.FSharp.Core;
using System;
using System.Text.RegularExpressions;

namespace FSharp.Domain.Shipping.Cargo
{
  [CompilationMapping(SourceConstructFlags.ObjectType)]
  [Serializable]
  public class UnLocode
  {
    internal string value;

    public UnLocode(string value)
    {
      UnLocode unLocode = this;
      this.value = value;
      if (!new Regex("[a-zA-Z]{2}[a-zA-Z2-9]{3}").Match(this.value).Success)
        throw new ArgumentException ...
    }

    public string Value
    {
      get
      {
        return this.value;
      }
    }
  }
}
```

- With implicit property

```csharp
// Decompiled with JetBrains decompiler
// Type: FSharp.Domain.Shipping.Cargo.UnLocode
// Assembly: Domain.FSharp, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
// MVID: E3F3DDE8-40DD-3B59-CE2C-3ED883E78338
// Assembly location: C:\akli\dev\CargoTracker2FSharp\Domain.FSharp\bin\Debug\netstandard2.0\Domain.FSharp.dll

using Microsoft.FSharp.Core;
using System;
using System.Text.RegularExpressions;

namespace FSharp.Domain.Shipping.Cargo
{
  [CompilationMapping(SourceConstructFlags.ObjectType)]
  [Serializable]
  public class UnLocode
  {
    internal string Value@;

    public UnLocode(string value)
    {
      UnLocode unLocode = this;
      if (!new Regex("[a-zA-Z]{2}[a-zA-Z2-9]{3}").Match(value).Success)
        throw new ArgumentException ...
      this.Value@ = value;
    }

    public string Value
    {
      get
      {
        return this.Value@;
      }
    }
  }
}
```

When you don't have invariants to check you self-indentifiers.

Next was `Location` class

The location constructor had a null guard at the constructor for UnLocode but for fsharp null is not a valid option.

Original code in C#

```csharp
public Location(UnLocode unLocode, string name)
{
    UnLocode = unLocode ?? throw new ArgumentNullException(nameof(unLocode));

    if (String.IsNullOrWhiteSpace(name))
        throw new ArgumentException("Provided name is not valid", "name");

    Name = name;
}
```

No null guard in F#

```fsharp
type Location(unlocode: UnLocode, name: string) =

    do
        if String.IsNullOrWhiteSpace(name) then raise (ArgumentException("Provided name is not valid", "name"))

    member val UnLocode = unlocode
    member val Name = name
```
