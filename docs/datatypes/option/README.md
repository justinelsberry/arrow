---
layout: docs
title: Option
permalink: /docs/datatypes/option/
video: 5SFTbphderE
---

## Option

{:.beginner}
beginner

If you have worked with Java at all in the past, it is very likely that you have come across a `NullPointerException` at some time (other languages will throw similarly named errors in such a case). Usually this happens because some method returns `null` when you were not expecting it and thus not dealing with that possibility in your client code. A value of `null` is often abused to represent an absent optional value.
Kotlin tries to solve the problem by getting rid of `null` values altogether and providing its own special syntax [Null-safety machinery based on `?`](https://kotlinlang.org/docs/reference/null-safety.html).

Arrow models the absence of values through the `Option` datatype similar to how Scala, Haskell and other FP languages handle optional values.

`Option<A>` is a container for an optional value of type `A`. If the value of type `A` is present, the `Option<A>` is an instance of `Some<A>`, containing the present value of type `A`. If the value is absent, the `Option<A>` is the object `None`.

```kotlin
import arrow.*
import arrow.core.*

val someValue: Option<String> = Some("I am wrapped in something")
someValue
// Some(I am wrapped in something)
```

```kotlin
val emptyValue: Option<String> = None
emptyValue
// None
```

Let's write a function that may or not give us a string, thus returning `Option<String>`:

```kotlin
fun maybeItWillReturnSomething(flag: Boolean): Option<String> =
   if (flag) Some("Found value") else None
```

Using `getOrElse` we can provide a default value `"No value"` when the optional argument `None` does not exist:

```kotlin
val value1 = maybeItWillReturnSomething(true)
val value2 = maybeItWillReturnSomething(false)
```

```kotlin
value1.getOrElse { "No value" }
// Found value
```

```kotlin
value2.getOrElse { "No value" }
// No value
```

Checking whether option has value:

```kotlin
value1 is None
// false
```

```kotlin
value2 is None
// true
```

Option can also be used with when statements:

```kotlin
val someValue: Option<Double> = Some(20.0)
val value = when(someValue) {
   is Some -> someValue.t
   is None -> 0.0
}
value
// 20.0
```

```kotlin
val noValue: Option<Double> = None
val value = when(noValue) {
   is Some -> noValue.t
   is None -> 0.0
}
value
// 0.0
```

An alternative for pattern matching is performing Functor/Foldable style operations. This is possible because an option could be looked at as a collection or foldable structure with either one or zero elements.

One of these operations is `map`. This operation allows us to map the inner value to a different type while preserving the option:

```kotlin
val number: Option<Int> = Some(3)
val noNumber: Option<Int> = None
val mappedResult1 = number.map { it * 1.5 }
val mappedResult2 = noNumber.map { it * 1.5 }
```

```kotlin
mappedResult1
// Some(4.5)
```

```kotlin
mappedResult2
// None
```

Another operation is `fold`. This operation will extract the value from the option, or provide a default if the value is `None`

```kotlin
number.fold({ 1 }, { it * 3 })
// 9
```

```kotlin
noNumber.fold({ 1 }, { it * 3 })
// 1
```

Arrow also adds syntax to all datatypes so you can easily lift them into the context of `Option` where needed.

```kotlin
1.some()
// Some(1)
```

```kotlin
none<String>()
// None
```

```kotlin
val nullableValue: String? = null
nullableValue.toOption()
// None
```

```kotlin
val nullableValue: String? = "Hello"
nullableValue.toOption()
// Some(Hello)
```

Arrow contains `Option` instances for many useful typeclasses that allows you to use and transform optional values

[`Functor`]({{ '/docs/typeclasses/functor/' | relative_url }})

Transforming the inner contents

```kotlin
import arrow.typeclasses.*
import arrow.instances.*

Option.functor().run {
  Some(1).map { it + 1 }
}
// Some(2)
```

[`Applicative`]({{ '/docs/typeclasses/applicative/' | relative_url }})

Computing over independent values

```kotlin
ForOption extensions {
  tupled(Some(1), Some("Hello"), Some(20.0))
}
// Some(Tuple3(a=1, b=Hello, c=20.0))
```

[`Monad`]({{ '/docs/typeclasses/monad/' | relative_url }})

Computing over dependent values ignoring absence

```kotlin
ForOption extensions {
  binding {
   val a = Some(1).bind()
   val b = Some(1 + a).bind()
   val c = Some(1 + b).bind()
   a + b + c
  }
}
//Some(value=6)
```

```kotlin
ForOption extensions {
  binding {
   val x = none<Int>().bind()
   val y = Some(1 + x).bind()
   val z = Some(1 + y).bind()
   x + y + z
  }
}
//None
```

## Available Instances:

* [Show]({{ '/docs/typeclasses/show' | relative_url }})
* [Eq]({{ '/docs/typeclasses/eq' | relative_url }})
* [Applicative]({{ '/docs/typeclasses/applicative' | relative_url }})
* [ApplicativeError]({{ '/docs/typeclasses/applicativeerror' | relative_url }})
* [Foldable]({{ '/docs/typeclasses/foldable' | relative_url }})
* [Functor]({{ '/docs/typeclasses/functor' | relative_url }})
* [Monad]({{ '/docs/typeclasses/monad' | relative_url }})
* [MonadError]({{ '/docs/typeclasses/monaderror' | relative_url }})
* [MonadFilter]({{ '/docs/typeclasses/monadfilter' | relative_url }})
* [Traverse]({{ '/docs/typeclasses/traverse' | relative_url }})
* [TraverseFilter]({{ '/docs/typeclasses/traversefilter' | relative_url }})
* [Each]({{ '/docs/optics/each' | relative_url }})

## Credits

Contents partially adapted from [Scala Exercises Option Tutorial](https://www.scala-exercises.org/std_lib/options)
Originally based on the Scala Koans.
