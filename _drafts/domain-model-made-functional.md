---
layout: post
title: Domain Modeling made functional
---

## Things that I liked

### Modeling workflows with functions

```fsharp
type ValidateOrder = UnvalidateOrder -> ValidatedOrder
```

```fsharp
type PlaceOrder = UnvalidateOrder -> PlaceOrderEvents
```

## Nice questions for the domain experts.

Good questions for the the domain expert,

```fsharp
type Order = {
    CustomerInfo: CustomerInfo
    ShippingAddress: ShippingAddress
    BillingAddress: BillingAddress
    OrderLines: OrderLine list
    AmountToBill: ...
}
```

Q: Is ShippingAddress and BillingAddress the same?

Q: What type shoud we use for AmountToBill?
