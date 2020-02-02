---
layout: post
title: Port Cargo Tracker to FSharp - Phase 1
---

#### Refereneces

[Domain Modeling Made Functional](https://pragprog.com/book/swdddf/domain-modeling-made-functional)

TrackingId is a value object for this we use the techinque for
`Modeling Simple Values`

From

```fsharp
[<AllowNullLiteral>]
type TrackingId(value: Guid) =
    member this.Value = value
    override this.GetHashCode() =
        hash (value)
    override this.Equals(other) =
        match other with
        | :? TrackingId as o -> (value) = (o.Value)
        | _ -> false
```

To

```fsharp
type TrackingId = TrackingId of Guid
```

Leg is a compound value and is represented as records

From

```fsharp
[<AllowNullLiteral>]
type Leg(voyage: VoyageNumber, loadLocation: UnLocode, unloadLocation: UnLocode, loadTime: DateTime, unloadTime: DateTime) =

    do
        if isNull voyage then raise <| ArgumentNullException "voyage"
        if isNull loadLocation then raise <| ArgumentNullException "loadLocation"
        if isNull unloadLocation then raise <| ArgumentNullException "unloadLocation"
        if loadTime >= unloadTime then raise <| ArgumentException "unloadTime should be later than loadTime"

    member val Voyage = voyage
    member val LoadLocation = loadLocation
    member val UnloadLocation = unloadLocation
    member val LoadTime = loadTime
    member val UnloadTime = unloadTime
```

To

```fsharp
type Leg = {
    Voyage: VoyageNumber
    LoadLocation: UnLocode
    UnloadLocation: UnLocode
    LoadTime: DateTime
    UnloadTime: DateTime
}
```

> TODO: Explain what is integrity and what is consistency in trusted domains.

Integrity in simple values is ensured by enforcing constrains.
In F# we enforce constrains by making the constructor private and have a seperate function that creates valid values. The function returns a `Result` type to return Error or Success.

From

```fsharp
[<AllowNullLiteral>]
type UnLocode(value: string) =

    do
        if isNull value then raise <| ArgumentNullException "value"

    do
        let pattern = Regex "[a-zA-Z]{2}[a-zA-Z2-9]{3}"
        let matches = pattern.Match value
        if not matches.Success then raise <| ArgumentException("Provided value is not a valid UnLocode", "value")

    member val Value = value
    member this.GetHashCode() = hash value
    member this.Equals(other) =
        match other with
        | :? UnLocode as o -> value = o.Value
        | _ -> false
```

To

```fsharp
module UnLocode =
    let create value =
        let pattern = Regex "[a-zA-Z]{2}[a-zA-Z2-9]{3}"
        let matches = pattern.Match value
        if String.IsNullOrWhiteSpace value then Error "Unlocode cannot be empty or null."
        else if not matches.Success then Error "Provided value is not a valid UnLocode"
        else Ok (UnLocode value)
```

> TODO: Examine other ways to hide constructors like signature files.
