---
layout: docs
title: Fold
permalink: /docs/optics/fold/
---

## Fold

{:.beginner}
beginner

A `Fold` is an optic that can see into a structure and get 0 to N foci.
It is a generalisation of an instance of [`Foldable`](/docs/typeclasses/foldable).

Creating a `Fold` can be done by manually defining `foldMap`.

```kotlin
import arrow.data.*
import arrow.optics.*
import arrow.typeclasses.*
import arrow.instances.*

fun <T> nullableFold(): Fold<T?, T> = object : Fold<T?, T> {
    override fun <R> foldMap(M: Monoid<R>, s: T?, f: (T) -> R): R =
        s?.let(f) ?: M.empty()
}
```

Or you can get a `Fold` from any existing `Foldable`.

```kotlin
val nonEmptyIntFold: Fold<NonEmptyListOf<Int>, Int> = Fold.fromFoldable(NonEmptyList.foldable())
```

`Fold` has a similar API as `Foldable` but because it's defined in terms of `foldMap` there are no associative fold functions available.

```kotlin
nullableFold<Int>().isEmpty(null)
// true
```
```kotlin
nonEmptyIntFold.combineAll(Int.monoid(), NonEmptyList.of(1, 2, 3))
// 6
```
```kotlin
nullableFold<Int>().headOption(null)
// None
```
```kotlin
nonEmptyIntFold.headOption(NonEmptyList.of(1, 2, 3, 4))
// Some(1)
```

## Composition

Composing `Fold` can be used for accessing foci in nested structures.

```kotlin
val nestedNelFold: Fold<NonEmptyListOf<NonEmptyListOf<Int>>, NonEmptyListOf<Int>> = Fold.fromFoldable(NonEmptyList.foldable())

val nestedNel = NonEmptyList.of(1, 2, 3, 4).map {
    NonEmptyList.of(it, it)
}

(nestedNelFold compose nonEmptyIntFold).getAll(nestedNel)
// ListK(list=[1, 1, 2, 2, 3, 3, 4, 4])
```

`Fold` can be composed with all optics but `Setter` and results in the following optics.

|   | Iso | Lens | Prism |Optional | Getter | Setter | Fold | Traversal |
| --- | --- | --- | --- |--- | --- | --- | --- | --- |
| Fold | Fold | Fold | Fold | Fold | Fold | X | Fold | Fold |