---
layout: post
title: Domain modeling made functional workshop
---

Antonios Klimis  2:09 PM
May I ask a question about a side exercise I'm doing to learn F#?

Scott Wlaschin  2:11 PM
Sure!

Antonios Klimis  2:12 PM
I'm converting the cargo tracker of Erick into F# style by reading your book.
2:12
I have concluded to following types
type BookCargo = UnvalidatedBookCargo -> Result<CargoBooked, BookCargoError>
type ValidateBookCargo = UnvalidatedLocation -> Result<ValidatedBookCargo, ValidationError>
type Booking = ValidatedBookCargo -> BookedCargo
(edited)
2:16
Workflow
image.png 
image.png


2:17
The Cargo type is been marked as NotRouted when initially is booked.
Routing Statue type is:
type RoutingStatus =
    | NotRouted = 0
    | Routed = 1
    | MisRouted = 2
(edited)
2:19
The BookedCargo is of type
type BookedCargo = {
    TrackingId: TrackingId
    Origin: ValidatedLocation
    Destination: ValidatedLocation
    ArrivalDeadline: ValidatedArrivalDeadline
}
(edited)
2:19
And somehow I have to force the RoutingStatus to be NotRoute
2:20
Here is the whole code: https://github.com/klimisa/CargoTracker2FSharp/blob/3ecba8e1a82acc2cd774715072983ed69d083954/Domain.FSharp/Shipping.Types.fs (edited) 
2:20
Any ideas how to force that into the model?

Scott Wlaschin  2:23 PM
I'm not sure I understand the question :slightly_smiling_face:
2:24
You have to force the RoutingStatus to be NotRouted ?

Antonios Klimis  2:24 PM
Yep on the BookedCargo Type
2:25
Oh, I have so many questions I wish this was happening in Milan :slightly_smiling_face: (edited) 

Scott Wlaschin  2:26 PM
I wish we could be in person too :slightly_smiling_face:
2:27
So, if the routing status can only have ONE possible value within a given record, then you surely don't need it at all?

Antonios Klimis  2:27 PM
It will be on the database.
2:28
As NotRouted at the beginning until you give it a Route, then it will be Routed

Scott Wlaschin  2:29 PM
In that case, I'd have a conversion the from domain object to a database object. The database object would have an extra field for the flag, which would always be set to the single value.
2:30
It's common to have a database table that represents different states. In the domain they are kept separate, but in the database they are all mixed up! :slightly_smiling_face:
2:31
When you load from the database, you might need to check whether the flags are still set correctly, otherwise it would be an (unexpected) error. If you trust the database, then you can just assume it's all OK.

Antonios Klimis  2:31 PM
That's great and then I will convert them back to appropriate domain type depending on the flags. Thanks a lot!
May I ask another question? (edited) 

Scott Wlaschin  2:32 PM
Sure!

Antonios Klimis  2:43 PM
The UnvalidateBookCargo type is the input from the Book Cargo Form
type UnvalidatedBookCargo = {
    TrackingId: string
    UnvalidatedOrigin: UnvalidatedLocation
    UnvalidatedDestination: UnvalidatedLocation
    UnvalidatedArrivalDeadline: UnvalidatedArrivalDeadline
}
The Origin, Destination and ArrivalDeadLine in Eric Evans Book is called RouteSpecification which is for us a type.
Is it ok to have UnvalidatedRouteSpecification instead of the 3 fields? (edited) 
2:45
Or is it further down on the domain, for example, when I return a BookedCargo, with a `RouteSpecification` ? (edited) 

Scott Wlaschin  2:46 PM
Sure, that makes sense.

Antonios Klimis  2:46 PM
type BookedCargo = {
    TrackingId: TrackingId
    RouteSpecification: RouteSpecification
}
2:47
Which one the first or the second? :slightly_smiling_face: (edited) 

Scott Wlaschin  2:47 PM
For unvalidated data, you probably don't need to use wrapper types. Plain strings are fine, because they are not yet validated!
2:49
It sounds like "RouteSpecification" is a concept in the domain, in which case make it a type. You might start finding other uses for it too -- what Eric calls "refactoring for insight"

Antonios Klimis  2:52 PM
I will try to finish the example before the workshop so I can have more questions. I hope you don't mind.
I strongly believe in learning by doing but a mentor is always necessary. Thank for that!
Yes, RouteSpecification is a concept of the domain. (edited) 





2:54
Just to be sure I understood you correctly you recommend the Unvalidate type to be,
type UnvalidatedBookCargo = {
    TrackingId: string
    UnvalidatedOrigin: string
    UnvalidatedDestination: string
    UnvalidatedArrivalDeadline: string
}
Correct? (edited) 

Scott Wlaschin  2:56 PM
Yes, that looks fine. You don't need to name the field "UnvalidatedOrigin", I'd just call it "Origin". Since it's part of the UnvalidatedBookCargo it's obviously unvalidated :slightly_smiling_face:
:+1:
1


Antonios Klimis  3:01 PM
In the book you have UnvalidatedAddress but I can see now when to choose which.
You have a service AddressValidationService = UnvalidatedAddress -> ValidateAddress
I presume you created a type and not a string because you have a service in place, yes? (edited) 

Scott Wlaschin  3:05 PM
I think so. I can't remember :slightly_smiling_face:
3:06
I think I was trying to be very explicit and obvious there. In the real world I probably wouldn't be so strict :slightly_smiling_face:

Antonios Klimis  3:07 PM
Thanks for your time.
It really helped me!!

Scott Wlaschin  3:08 PM
Glad to help!

Antonios Klimis  3:11 PM
It takes a while to mind swift into functional programming, but type safety is really awesome and comes with lot's of goodies like live documention.

Scott Wlaschin  3:17 PM
Yes, it's nice when you get used to it!


----------------------------------------------------------

// =================================================
// Exercise C - redesign this type into two states.
// Can you guess what the states are from the flags?
// how does the refactored version help improve the documentation?
type Order_BeforeRefactoring =
   {
    OrderId: int
    IsPaid: bool
    PaidAmount: float option //only set when paid
    PaidDate: System.DateTime option //only set when paid
   }
type PaidOrder = {
    OrderId: int
    PaidAmount: float option //only set when paid
    PaidDate: System.DateTime option //only set when paid
}
type UnPaidOrder = {
    OrderId: int
}
type Order_AfterRefactoring =
    | PaidOrder of PaidOrder
    | UnpaidOrder of UnPaidOrder
Thanks @Enrico Meloni and @Scott Wlaschin
Q: Are we going to create distinct types for representing the constraint `only set when paid`?
(edited)

1:53
Let me look at that
1:56
Ok, the idea is that before refactoring, the PaidAmount and PaidDate are not always used, so they have to be optional.
But after refactoring into two cases, in one of the cases they are ALWAYS required and can be put together in a structure.
type OrderId = OrderId of int
type PaidAmount = float
type PaidDate = System.DateTime
type PaidOrderInfo = {
    OrderId : OrderId
    Amount : PaidAmount // no longer optional
    Date : PaidDate // no longer optional
    }
type Order_AfterRefactoring =
    | Unpaid of OrderId
    | Paid of PaidOrderInfo // PaidAmount and PaidDate only used in this case
1:56
Does this answer your question?

Antonios Klimis  2:00 PM
Yes, thanks a lot!
2:01
In the case of OrderId wasn't the only field needed we would have created a second type, yes? (edited) 

Scott Wlaschin  2:04 PM
Yes, exactly!