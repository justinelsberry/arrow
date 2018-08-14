---
layout: docs
title: Syntax DSL
permalink: /docs/optics/dsl/
---

## Optics DSL

{:.beginner}
beginner

Arrow offers a Optics DSL to compose different Optics while improving ease of use and readability.
To avoid boilerplate Arrow will generate this property-like dsl using `@optics` annotation.

```kotlin
package com.example.domain

@optics data class Street(val number: Int, val name: String)
@optics data class Address(val city: String, val street: Street)
@optics data class Company(val name: String, val address: Address)
@optics data class Employee(val name: String, val company: Company?)
```

The DSL will be generated in the same package as your `data class` and can be used on the `Companion` of your class.

```kotlin
import arrow.optics.dsl.*
import com.example.domain.*
import arrow.optics.Optional

val john = Employee("John Doe", Company("Kategory", Address("Functional city", Street(42, "lambda street"))))

val optional: Optional<Employee, String> = Employee.company.address.street.name
optional.modify(john, String::toUpperCase)
// Employee(name=John Doe, company=Company(name=Kategory, address=Address(city=Functional city, street=Street(number=42, name=LAMBDA STREET))))
```

Arrow can also generate dsl for a `sealed class` which can be helpful to reduce boilerplate code, or improve readability.

```kotlin
package com.example.domain

@optics sealed class NetworkResult
@optics data class Success(val content: String): NetworkResult()
@optics sealed class NetworkError : NetworkResult()
@optics data class HttpError(val message: String): NetworkError()
object TimeoutError: NetworkError()
```

Let's imagine we have a function `f` of type `(HttpError) -> HttpError` and we want to invoke it on the `NetworkResult`.

```kotlin
val networkResult: NetworkResult = HttpError("boom!")
val f: (String) -> String = String::toUpperCase

when (networkResult) {
  is HttpError -> networkResult.copy(f(networkResult.message))
  else -> networkResult
}
```

We can rewrite this code with our generated dsl.

```kotlin
NetworkResult.networkError.httpError.message.modify(networkResult, f)
// HttpError(message=BOOM!)
```

The DSL also has special support for [Each]({{ '/docs/optics/each' | relative_url }}), [At]({{ '/docs/optics/at' | relative_url }}) and [Index]({{ '/docs/optics/index' | relative_url }}).

### Each

`Each` can be used to focus into a structure `S` and see all its foci `A`. Here we focus into all `Employee`s in the `Employees`.

```kotlin
@optics data class Employees(val employees: ListK<Employee>)
```

```kotlin
import arrow.data.*

val jane = Employee("Jane Doe", Company("Kategory", Address("Functional city", Street(42, "lambda street"))))
val employees = Employees(listOf(john, jane).k())

Employees.employees.every(ListK.each()).company.address.street.name.modify(employees, String::capitalize)
// Employees(employees=ListK(list=[Employee(name=John Doe, company=Company(name=Kategory, address=Address(city=Functional city, street=Street(number=42, name=Lambda street)))), Employee(name=Jane Doe, company=Company(name=Kategory, address=Address(city=Functional city, street=Street(number=42, name=Lambda street))))]))
```

If you are in the scope of `Each` you don't need to specify the instance.

```kotlin
ListK.each<Employee>().run {
  Employees.employees.every.company.address.street.name.modify(employees, String::capitalize)
}
// Employees(employees=ListK(list=[Employee(name=John Doe, company=Company(name=Kategory, address=Address(city=Functional city, street=Street(number=42, name=Lambda street)))), Employee(name=Jane Doe, company=Company(name=Kategory, address=Address(city=Functional city, street=Street(number=42, name=Lambda street))))]))
```

### At

`At` can be used to focus in `A` at a given index `I` for a given structure `S`.

```kotlin
@optics data class Db(val content: MapK<Int, String>)
```

Here we focus into the value of a given key in `MapK`.

```kotlin
val db = Db(mapOf(
  1 to "one",
  2 to "two",
  3 to "three"
).k())

Db.content.at(MapK.at(), 2).some.modify(db, String::reversed)
// Db(content=MapK(map={1=one, 2=owt, 3=three}))
```

If you are in the scope of `At` you don't need to specify the instance.

```kotlin
MapK.at<Int, String>().run {
  Db.content.at(2).some.modify(db, String::reversed)
}
// Db(content=MapK(map={1=one, 2=owt, 3=three}))
```

### Index

`Index` can be used to operate on a structure `S` that can index `A` by an index `I`.
i.e. a `List<Employee>` by its index position or a `Map<K, V>` by its keys `K`.


```kotlin
val updatedJohn = Employees.employees.index(ListK.index(), 0).company.address.street.name.modify(employees, String::capitalize)
updatedJohn
// Employees(employees=ListK(list=[Employee(name=John Doe, company=Company(name=Kategory, address=Address(city=Functional city, street=Street(number=42, name=Lambda street)))), Employee(name=Jane Doe, company=Company(name=Kategory, address=Address(city=Functional city, street=Street(number=42, name=lambda street))))]))
```

In the scope of `Index` you don't need to specify the instance and so we can enable `operator fun get` syntax.

```kotlin
ListK.index<Employee>().run {
  Employees.employees[0].company.address.street.name.getOption(updatedJohn)
}
// Some(Lambda street)
```

Since [Index]({{ '/docs/optics/index' | relative_url }}) returns an [Optional]({{ '/docs/optics/optional' | relative_url }}) `index` and `[]` are safe operations.

```kotlin
ListK.index<Employee>().run {
  Employees.employees[2].company.address.street.name.getOption(employees)
}
// None
```
