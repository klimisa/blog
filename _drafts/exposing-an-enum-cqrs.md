---
layout: post
title: Enums in CQRS
---

Every programmer have used Enums in past also the two mainstream languages C# and Java have great support. CQRS is also well know pattern, it has been around the last decade.

But how do you handle and expose Enums in CQRS?

I will try to address a posible design on how to handle and approach enums in CQRS.

## Setting the scene

The two sides of the equation in CQRS are the _write_ and the _read_ rides. The write side it domain driven and we can create a **model** that describes the domain problem at hand. The read side it's display on data and it's usually consumer driven, so here we don't have model but rather a **schema** that we share with our consumers.

## Enum values as primitives on the write side

```c#
public class Planet : ValueObject
{
    public static readonly Planet Mercury = new Planet("Mercury", 3.302e+23, 2.439e6);
    public static readonly Planet Venus = new Planet("Venus", 4.869e+24, 6.052e6);
    public static readonly Planet Earth = new Planet("Earth", 5.975e+24, 6.378e6);
    public static readonly Planet Mars = new Planet("Mars", 6.419e+23, 3.393e6);
    public static readonly Planet Jupiter = new Planet("Jupiter", 1.899e+27, 7.149e7);
    public static readonly Planet Saturn = new Planet("Saturn", 5.685e+26, 6.027e7);
    public static readonly Planet Uranus = new Planet("Uranus", 8.683e+25, 2.556e7);
    public static readonly Planet Neptune = new Planet("Neptune", 1.024e+26, 2.477e7);
    public static readonly Planet Pluto = new Planet("Pluto", 1.27e+22, 1.137e6);

    public static IEnumerable<Planet> All
    {
        get
        {
            yield return Mercury;
            yield return Venus;
            yield return Earth;
            yield return Mars;
            yield return Jupiter;
            yield return Saturn;
            yield return Uranus;
            yield return Neptune;
            yield return Pluto;
        }
    }

    public string Name { get; }
    public double Mass { get; }
    public double Radius { get; }
    public double SurfaceGravity => G * Mass / (Radius * Radius);

    private static double G = 6.67300E-11;

    private Planet(string name, double mass, double radius) =>
        (Name, Mass, Radius) = (name, mass, radius);

    public static Planet From(string name) =>
        All.FirstOrDefault(p => p.Name == name)
        ?? throw new ArgumentOutOfRangeException(nameof(name));

    public double SurfaceWeight(double mass) => mass * SurfaceGravity;

    public override string ToString() => Name;

    protected override IEnumerable<object> GetAtomicValues()
    {
        yield return Name;
    }
}
```

The equation starts at how we pass the enum values from our api endpoint. We will use string for that which we will validate when

I have an enum in write-side and I wanna expose the values in the read-side.

I can think of two solutions,

1. If I want both W&R side to evolve together then I create a package which share the same definition assuming that I use the same technology for both side.
2. If I want them to evolve separately then I duplicate the enum values in the read-side.

But the actual decision it also depends on the following,

- Are the values of the enum change frequently
- Are W&R handled by the same team
- Are they use the same technology stack

Can you please share your experience?

## Yves response

[Link](https://ddd-cqrs-es.slack.com/archives/C405CBSE8/p1575204915032300)

I think there's a difference between the schema and the model. Exposing enums as part of a schema (side stepping the write vs read side for a moment) will depend on the encoding and schema definition language (a.k.a. IDL). Having a clear set of values to pick from makes it easy for consumers. But if consumers base themselves on documentation, defining a list of textual options to pick from will work too (regardless of whether the options are aliases for a numeric value or become part of the payload as is or become part of a binary encoding). Typed languages tend to suffer from name clashes more - e.g. the value of a model's value object may manifest itself as an enum in the code representation of the schema too. For this reason I prefer (in the case of json) a string on the wire and parsing that string into a value object as part of the application to domain translation. Doing input validation is a no brainer for me and it makes it less awkward than trying to parse / map an enum into a value object, though that is mainly a personal preference. I'll tend to have strongly typed static accessors on the value object to represent each one of the options, an All to list all the options, etc ... carrying the value forward in event sourcing will be done by putting a string on the event, in case of state / document based persistence, a property that could be modelled as a string (or binary or a number, whatever the convention I choose).

I tend not to share too much - beyond the message schema - between the read and write side. The (actually a) read side is mainly driven by consumers sitting on top of it - if exposing over http, the request / response model will come with its own schema and could very well opt to translate back into an enum that is associated with that schema. Any reuse you are seeing is accidental because the tech stack may be the same one. It's a bit more effort on the mapping side, granted. And there's nothing wrong with choosing to share the enum either - it's just coupling - whether bad or wrong only time will tell - if that area of the software never changes, nobody will win the argument.

The drivers for making that decision, whether to share or not, @antonios mentions are sane ones and would probably cross my mind too depending on the team and their maturity.

## Antonios response

Thanks, it is everything anyone will ever need in your post.
I keep,

Language definition

- _Model_ on Write Side
- _Schema_ on Read Side

Expose a string value on the wire parsed to Value Object (validation comes without saying)

Value Object with

- Strongly typed static accessors for each value
- All accessor for the list of values

Saved as string (or binary or a number)

Make a decision on whether to reuse an enum on the team and their maturity.

I'm not insane :stuck_out_tongue:

```

```
