---
layout: docs
title: Order
permalink: /docs/typeclasses/order/
---

## Order

{:.beginner}
beginner

The `Order` typeclass abstracts the ability to compare two instances of any object and determine their total order.
Depending on your needs this comparison can be structural -the content of the object-, referential -the memory address of the object-, based on an identity -like an Id field-, or any combination of the above.

It can be considered the typeclass equivalent of Java's `Comparable`.

### Main Combinators

#### F#compare

`fun F.compare(b: F): Int`

Compare [a] with [b]. Returns an Int whose sign is:
  * negative if `x < y`
  * zero     if `x = y`
  * positive if `x > y`

```kotlin
import arrow.*
import arrow.typeclasses.*
import arrow.instances.*

Int.order().run { 1.compare(2) }
// -1
```

#### F#lte / F#lt

Lesser than or equal to defines total order in a set, it compares two elements and returns true if they're equal or the first is lesser than the second.
It is the opposite of `gte`.

```kotlin
ForInt extensions { 
  1.lte(2) 
}
// true
```

#### F#gte / F#gt

Greater than or equal compares two elements and returns true if they're equal or the first is lesser than the second.
It is the opposite of `lte`.

```kotlin
ForInt extensions { 
  1.gte(2) 
}
// false
```

#### F#max / F#min

Compares two elements and respectively returns the maximum or minimum in respect to their order.

```kotlin
ForInt extensions { 
  1.min(2) 
}
// 1
```
```kotlin
ForInt extensions { 
  1.max(2) 
}
// 2
```

#### F#sort

Sorts the elements in a `Tuple2`

```kotlin
ForInt extensions { 
  1.sort(2) 
}
// Tuple2(a=2, b=1)
```

### Laws

Arrow provides `OrderLaws` in the form of test cases for internal verification of lawful instances and third party apps creating their own `Order` instances.

#### Creating your own `Order` instances

Order has a constructor to create an `Order` instance from a compare function `(F, F) -> Int`.

```kotlin

Order { a: Int, b: Int -> b - a }.run {
  1.lt(2)
}
// false
```

See [Deriving and creating custom typeclass]({{ '/docs/patterns/glossary' | relative_url }}) to provide your own `Order` instances for custom datatypes.
