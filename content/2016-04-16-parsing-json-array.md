---
title: Parsing a JSON array with multiple variants using Serde
date: 2016-04-16
---


Let's say we have the following JSON data:

    [ { "keyA": "valueA" }, { "keyB": "valueB" } ]

Our models would look like:

    struct Foo {
        bar: Vec<Bar>
    }

    enum Bar {
        A(TypeA), B(TypeB)
    }

    struct TypeA {
        keyA: String,
    }

    struct TypeB {
        keyB: String,
    }


Unfortunately, Serde will (de)serialize this to:


    [ { "A": { "keyA": "valueA" } }, { "B": { "keyB": "valueB" } ]


To get the desired result, we have to implement Serde's `Serializer` and `Deserializer`
traits. It's actually quite simple, all we have to do is to call the respective
methods on each sub-type. The serializer looks like:

    impl serde::Serialize for Bar {
        fn serialize<S>(&self, serializer: &mut S) -> Result<(), S::Error>
            where S: serde::Serializer
        {
            match self {
                &Bar::A(ref a) => try!(a.serialize::<S>(serializer)),
                &Bar::B(ref b) => try!(b.serialize::<S>(serializer)),
            };
            Ok(())
        }
    }

This is the (lazy) deserializer:

    use serde::de::Error;

    impl serde::Deserialize for Bar {
        fn deserialize<D>(deserializer: &mut D) -> Result<Self, D::Error>
            where D: serde::Deserializer
        {
            if let Ok(a) = TypeA::deserialize::<D>(deserializer) {
                Ok(Bar::A(a))
            } else if let Ok(b) = TypeB::deserialize::<D>(deserializer) {
                Ok(Bar::B(b))
            } else {
                Err(D::Error::unknown_variant(""))
            }
        }
    }

This is bascially a bruteforce parser (did I just invent that term?): It tries
all enum variants until it runs out of them and returns a error, which means quite
a lot of overhead when you have a lot of variants. But if you're running into
performance problems because of this, you should probably change the format of
your data.