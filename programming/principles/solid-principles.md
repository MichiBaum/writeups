# SOLID

SOLID is a mnemonic acronym for five design principles intended to make object-oriented designs more understandable, flexible, and maintainable.

These principles are:

S -> [Single-responsibility principle](#single-responsibility-principle) \
O -> [Open–closed principle](#open–closed-principle) \
L -> [Liskov substitution principle](#liskov-substitution-principle) \
I -> [Interface segregation principle](#interface-segregation-principle) \
D -> [Dependency inversion principle](#dependency-inversion-principle)


## Single-responsibility principle
Robert C. Martin, the originator of the term, expresses the principle as, "A class should have only one reason to change". Because of confusion around the word "reason", he later clarified his meaning in a blog post titled "The Single Responsibility Principle", in which he mentioned Separation of Concerns and stated that "Another wording for the Single Responsibility Principle is: Gather together the things that change for the same reasons. Separate those things that change for different reasons.". In some of his talks, he also argues that the principle is, in particular, about roles or actors. For example, while they might be the same person, the role of an accountant is different from a database administrator. Hence, each module should be responsible for each role.


## Open–closed principle
In object-oriented programming, the open–closed principle (OCP) states "software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification"; that is, such an entity can allow its behaviour to be extended without modifying its source code.

- A module will be said to be open if it is still available for extension. For example, it should be possible to add fields to the data structures it contains, or new elements to the set of functions it performs.
- A module will be said to be closed if [it] is available for use by other modules. This assumes that the module has been given a well-defined, stable description (the interface in the sense of information hiding).


## Liskov substitution principle
TODO


## Interface segregation principle
The interface segregation principle (ISP) states that no code should be forced to depend on methods it does not use. ISP splits interfaces that are very large into smaller and more specific ones so that clients will only have to know about the methods that are of interest to them. Such shrunken interfaces are also called role interfaces. ISP is intended to keep a system decoupled and thus easier to refactor, change, and redeploy.

Within object-oriented design, interfaces provide layers of abstraction that simplify code and create a barrier preventing coupling to dependencies. A system may become so coupled at multiple levels that it is no longer possible to make a change in one place without necessitating many additional changes. Using an interface or an abstract class can prevent this side effect.

## Dependency inversion principle
TODO