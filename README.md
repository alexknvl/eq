# eq
Sane Scala equality library.

## Motivation
Scala's default equality `==` is neither reflexive, symmetric, transitive, nor does it satisfy congruence or extensionality laws. Basically, it does not satisfy **any** laws at all. It is inconsistent with `equals` and in the future is likely to become even more inconsistent. It does not respect types, allowing you to compare completely unrelated types.

### The big three
Equality is an [equivalence relation](https://en.wikipedia.org/wiki/Equivalence_relation), which means that it has to satisfy three axioms:
 * a ~ a. ([Reflexivity](https://en.wikipedia.org/wiki/Reflexive_relation))
 * a ~ b if and only if b ~ a. ([Symmetry](https://en.wikipedia.org/wiki/Symmetric_relation))
 * if a ~ b and b ~ c then a ~ c. ([Transitivity](https://en.wikipedia.org/wiki/Transitive_relation))

```scala
// Congruence (even with respect to Scalazzi) is broken.
-0.0d == 0.0d // true
(1 / -0.0d) == (1 / 0.0d) // false

// Reflexivity is broken.
val a = java.lang.Double.longBitsToDouble(0x7ff8000000000000L)
val b = java.lang.Double.longBitsToDouble(0x7ff8000000000001L)
a == a // false
a == b // false

// Furthermore it is inconsistent with the default ordering.
// Equals is equally broken, just less so.
a.equals(b) // true
a.compare(b) // 0
a.equals(a) // true
a.compare(a) // 0

// == is inconsistent with equals
0 == 0L // true
0 equals 0L // false
(0 : Any) == (0L : Any) // true, but will likely be false in the future!
(0 : Any) equals (0L : Any) // false

// Transitivity is broken.
9007199254740992L == 9007199254740992.0 // true
9007199254740992.0 == 9007199254740993L //true
9007199254740992L == 9007199254740993L // false

123456789.toFloat // 1.23456792E8
123456789.toFloat == 123456789 // true
123456789 == 123456789.toFloat // true
123456788 == 123456789.toFloat // false
123456790 == 123456789.toFloat // true
```

### IEEE 754
https://codewords.recurse.com/issues/one/when-is-equality-transitive-and-other-floating-point-curiosities
https://github.com/rust-lang/rust/issues/10320
https://internals.rust-lang.org/t/avoiding-partialord-problems-by-introducing-fast-finite-floating-point-types/5376/12
https://www.reddit.com/r/haskell/comments/wbsd3/for_float_not_referentially_transparent/

### Types

```scala
// Doesn't respect types.
1 == 1L // true
(1.0, BigInt(1)) == ((1, 1.0)) // true
```

### Cooperative equality
https://github.com/scala/collection-strawman/issues/246
https://contributors.scala-lang.org/t/can-we-get-rid-of-cooperative-equality/1131/17

### Inequality `!=`

### Scalazzi
http://data.tmorris.net/talks/parametricity/4985cb8e6d8d9a24e32d98204526c8e3b9319e33/parametricity.pdf

* ~~null~~
* ~~exceptions~~
* ~~Type-casing (isInstanceOf)~~
* ~~Type-casting (asInstanceOf)~~
* ~~Side-effects~~
* ~~equals/toString/hashCode~~
* ~~notify/wait~~
* ~~classOf/.getClass~~
* General recursion

### Extensionality
[Extensionality](https://en.wikipedia.org/wiki/Extensionality)
https://www.reddit.com/r/haskell/comments/360mir/comparing_functions_for_equality/

### Identity

### Treatment of `null`
```scala
// null is special
null.asInstanceOf[Int] == 0 // true
((null, 1).asInstanceOf[(Int, Int)]) == ((0, 1)) // false
```

### Relation to `eq` and `equals`

### Relation to `.type`

#### Typeclass coherence
https://github.com/scalaz/scalaz/issues/671

### Multiversal equality
https://github.com/lampepfl/dotty/issues/1247
https://github.com/scala/collection-strawman/issues/100

## Equivalence vs Equality
https://github.com/TomasMikula/hasheq/blob/master/Equivalence-Equality.md
https://en.wikipedia.org/wiki/Equivalence_relation
https://en.wikipedia.org/wiki/Isomorphism
https://en.wikipedia.org/wiki/First-order_logic#Equality_and_its_axioms
https://ncatlab.org/nlab/show/equality

### Hashcode
```scala
scala> val p = new java.lang.Float(1)
p: Float = 1.0

scala> p.hashCode
res0: Int = 1065353216

scala> p.##
res1: Int = 1
```

### BUGS, there are plenty
Mistreatment of equality leads to numerous issues.
https://github.com/scalaz/scalaz/issues/1138, https://github.com/scalaz/scalaz/issues/1156, https://issues.scala-lang.org/browse/SI-2574, http://www.scala-lang.org/old/node/4254, https://github.com/scala/bug/issues/6492, https://github.com/scala/bug/issues/6331, https://github.com/scala/bug/issues/9671, https://github.com/scala/bug/issues/8998, https://github.com/scala/bug/issues/10511, https://github.com/typelevel/cats/issues/1909, https://github.com/typelevel/cats/issues/123, https://github.com/typelevel/algebra/issues/137, https://github.com/typelevel/cats/issues/1359, https://twitter.com/copumpkin/status/694662182873632772, https://github.com/typelevel/cats/issues/855, https://github.com/circe/circe/issues/187, https://github.com/typelevel/cats/issues/589, https://github.com/typelevel/cats/issues/1710, https://github.com/typelevel/cats/pull/1712, https://github.com/typelevel/cats/issues/1831

## Solutions
https://github.com/alexknvl/leibniz/blob/master/src/main/scala/leibniz/Eq.scala is broken ATM
