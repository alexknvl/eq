# eq
Sane Scala equality library.

## Motivation
Scala's default equality `==` is neither reflexive, symmetric, transitive, nor does it satisfy congruence or extensionality laws. Basically, it does not satisfy **any** laws at all.

