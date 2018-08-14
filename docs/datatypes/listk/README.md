---
layout: docs
title: ListK
permalink: /docs/datatypes/listk/
---

## ListK

{:.beginner}
beginner

ListK wraps over the platform `List` type to make it a [type constructor](/docs/patterns/glossary/#type-constructors).

It can be created from Kotlin List type with a convenient `k()` function.

```kotlin
import arrow.*
import arrow.data.*

listOf(1, 2, 3).k()
// ListK(list=[1, 2, 3])
```

and unwrapped with the field `list`.

```kotlin
listOf(1, 2, 3).k().list
// [1, 2, 3]
```

ListK implements operators from many useful typeclasses.

For instance, it has `combineK` from the [`SemigroupK`](/docs/typeclasses/semigroupk/) typeclass.

It can be used to cheaply combine two lists:

```kotlin
val hello = listOf('h', 'e', 'l', 'l', 'o').k()
val commaSpace = listOf(',', ' ').k()
val world = listOf('w', 'o', 'r', 'l', 'd').k()

hello.combineK(commaSpace).combineK(world)
// ListK(list=[h, e, l, l, o, ,,  , w, o, r, l, d])
```

The functions `traverse` and `sequence` come from [`Traverse`](/docs/typeclasses/traverse/).

Traversing a list creates a new container [`Kind<F, A>`](/docs/patterns/glossary/#type-constructors) by combining the result of a function applied to each element:

```kotlin
import arrow.core.*
import arrow.instances.*

val numbers = listOf(Math.random(), Math.random(), Math.random()).k()
numbers.traverse(Option.applicative()) { if (it > 0.5) Some(it) else None }
// None
```

and complements the convenient function `sequence()` that converts a list of `ListK<Kind<F, A>>` into a `Kind<F, ListK<A>>`:

```kotlin
fun andAnother() = Some(Math.random())

val requests = listOf(Some(Math.random()), andAnother(), andAnother()).k()
requests.sequence(Option.applicative()).fix()
// Some(ListK(list=[0.5588884822931507, 0.36496971987303417, 0.7158346747942148]))
```

If you want to aggregate the elements of a list into any other value you can use `foldLeft` and `foldRight` from [`Foldable`](/docs/typeclasses/foldable).

Folding a list into a new value, `String` in this case, starting with an initial value and a combine function:

```kotlin
listOf('a', 'b', 'c', 'd', 'e').k().foldLeft("-> ") { x, y -> x + y }
// -> abcde
```

Or you can apply a list of transformations using `ap` from [`Applicative`](/docs/typeclasses/applicative/).

```kotlin
import arrow.instances.*
ForListK extensions {
  listOf(1, 2, 3).k()
    .ap(listOf({ x: Int -> x + 10}, { x: Int -> x * 2}).k())
}
// ListK(list=[11, 12, 13, 2, 4, 6])
```

## Available Instances

* [Show]({{ '/docs/typeclasses/show' | relative_url }})
* [Eq]({{ '/docs/typeclasses/eq' | relative_url }})
* [Functor]({{ '/docs/typeclasses/functor' | relative_url }})
* [Monad]({{ '/docs/typeclasses/monad' | relative_url }})
* [Applicative]({{ '/docs/typeclasses/applicative' | relative_url }})
* [Foldable]({{ '/docs/typeclasses/foldable' | relative_url }})
* [Traverse]({{ '/docs/typeclasses/traverse' | relative_url }})
* [Monoid]({{ '/docs/typeclasses/monoid' | relative_url }})
* [MonoidK]({{ '/docs/typeclasses/monoidk' | relative_url }})
* [Semigroup]({{ '/docs/typeclasses/semigroup' | relative_url }})
* [SemigroupK]({{ '/docs/typeclasses/semigroupk' | relative_url }})
* [FunctorFilter]({{ '/docs/typeclasses/functorfilter' | relative_url }})
* [MonadFilter]({{ '/docs/typeclasses/monadfilter' | relative_url }})
* [MonadCombine]({{ '/docs/typeclasses/monadcombine' | relative_url }})
* [Each]({{ '/docs/optics/each' | relative_url }})
* [Index]({{ '/docs/optics/index' | relative_url }})
* [FilterIndex]({{ '/docs/optics/filterindex' | relative_url }})
