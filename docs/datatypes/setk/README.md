---
layout: docs
title: SetK
permalink: /docs/datatypes/setk/
video: xtnyCqeLI_4
---

## SetK

{:.beginner}
beginner

SetK(Kinded Wrapper) is a higher kinded wrapper around the the Set collection interface.

It can be created from the Kotlin Set type with a convient `k()` function.

```kotlin
import arrow.*
import arrow.core.*
import arrow.data.*

setOf(1, 2, 5, 3, 2).k()
// SetK(set=[1, 2, 5, 3])
```

It can also be initialized with the following:

```kotlin
SetK(setOf(1, 2, 5, 3, 2))
// SetK(set=[1, 2, 5, 3])
```
or
```kotlin
SetK.just(1)
// SetK(set=[1])
```

given the following:
```kotlin
val oddNumbers = setOf( -11, 1, 3, 5, 7, 9).k()
val evenNumbers = setOf(-2, 4, 6, 8, 10).k()
val integers = setOf(-5, -4, -3, -2, -1, 0, 1, 2, 3, 4, 5).k()
```
SetK derives the following typeclasses:

[`Semigroup`](/docs/typeclasses/semigroup/) and [`SemigroupK`](/docs/typeclasses/semigroupk/):

```kotlin
val numbers = oddNumbers.combineK(evenNumbers.combineK(integers))
numbers
// SetK(set=[-11, 1, 3, 5, 7, 9, -2, 4, 6, 8, 10, -5, -4, -3, -1, 0, 2])
```
```kotlin
evenNumbers.combineK(integers).combineK(oddNumbers)
// SetK(set=[-2, 4, 6, 8, 10, -5, -4, -3, -1, 0, 1, 2, 3, 5, -11, 7, 9])
```

[`Monoid`](/docs/typeclasses/monoid/) and [`MonoidK`](/docs/typeclasses/monoidk/):
```kotlin
numbers.combineK(SetK.empty()) 
// SetK(set=[-11, 1, 3, 5, 7, 9, -2, 4, 6, 8, 10, -5, -4, -3, -1, 0, 2])
```

[`Foldable`](/docs/typeclasses/foldable/):
```kotlin
numbers.foldLeft(0) {sum, number -> sum + (number * number)}
// 561
```

## Available Instances

* [Show]({{ '/docs/typeclasses/show' | relative_url }})
* [Eq]({{ '/docs/typeclasses/eq' | relative_url }})
* [Foldable]({{ '/docs/typeclasses/foldable' | relative_url }})
* [Monoid]({{ '/docs/typeclasses/monoid' | relative_url }})
* [MonoidK]({{ '/docs/typeclasses/monoidk' | relative_url }})
* [Semigroup]({{ '/docs/typeclasses/semigroup' | relative_url }})
* [SemigroupK]({{ '/docs/typeclasses/semigroupk' | relative_url }})
* [At]({{ '/docs/optics/at' | relative_url }})
