# eq
Sane Scala equality library.

## Motivation
Scala's default equality `==` is neither reflexive, symmetric, transitive, nor does it satisfy congruence or extensionality laws. Basically, it does not satisfy **any** laws at all. It is inconsistent with `equals` and in the future is likely to become even more inconsistent. It does not respect types, allowing you to compare completely unrelated types.

```scala
// Doesn't respect types.
1 == 1L // true
(1.0, BigInt(1)) == ((1, 1.0)) // true

// null is special
null.asInstanceOf[Int] == 0 // true
((null, 1).asInstanceOf[(Int, Int)]) == ((0, 1)) // false

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
```
