# Design Patterns

ref: Dive-into-design-patterns

## Object-Oriented Patterns

- Each pattern describes a problem which occurs over and over again in our environment and then describes the core of the solution to that problem in such a way that you can use this solution a million times
  over without ever doing it the same way twice.
- Solutions expressed in objects and interfaces.
- Pattern elements
  - Pattern name
  - Problem
  - Solution
  - Consequences
- Sections of pattern description
  - Intent - describes both the problem and the solution.
  - Motivation - 
  - Structure
  - Code example
  
## Classification of Patterns

- most basic and low-level patterns are called idioms.
- Most universal and high-level patterns are architectural.
- Main categories
  - Creational Patterns - provide object creation mechanisms  that increase flexibility and reuse of existing code.
  - Structural Patterns - explain how to assemble objects and classes into larger structures, while keeping structuers flexible and efficient.
  - Behavioral Patterns - take care of effective communication and assigment of responsibilities between objects.
  
## Features of Good design

- Code Reuse
  - preserve cost and time when building s/w.
  - 3 levels of reuse
    - lowest level - resue classes, class libraries
    - highest level - frameworks
    - middle level - patterns, design patterns are smaller than frameworks
- Extensibility

## Design Principles

- Encapsulate what varies
  - identify aspects of your application that vary and separate them from what stays the same.
  - gola is to minimise effect caused by changes.
  - encapsulate on method level(tax change example).
  - encapsulate on class level
- Program to an interface not an implementation
  - depend on abstractions and not on concrete classes.
  - one tell if a design is flexible enough if you can easily extend it without breaking any existing code.
- Favor composition over Inheritance
  - list problems related to inheritance.

## SOLID Principles

- design principles intended to make software designs more understandable, flexible and maintainable.
- Single Responsibility Principle
  - a class should have just one reason to change.
  - make a class responsible for one functionality and then make it encapsulated.
- Open/closed principle
  - classes should be open for extension but closed for modification
  - keep existing code from breaking when you implement new features.
- Liskov substitution principle
  - when extending a class, remember you should be able to pass objects of the subclass in place of objects of the parent class without breaking client code.
  - sublass should remain compatible with behaviour of the superclass, when overriding a method, extend the base behaviour rather than replace entirely.
  - critical for libraries and frameworks
  - checks
    - paramater types should match.
    - return type too should match.
    - should throw different exceptions
    - shouldnt strengthen or weaken pre/post-conditions
    - invariants of a superclass must be preserved
    - subclass shouldnt change values of private fields of the superclass.
- Interface Segragation Principle
  - clients shouldn't be forced to depend on methods they do not use.
  - make your interfaces narrow enough that client classes dont have to implement behvaiours they dont need, break down fat interfaces into more granular and specific ones.
- Dependency Inversion Principle
  - high-level classes shouldnt depend on low-level classes, both should depend on abstractions, and abstractions shouldn't depend on details but vice-versa.
  - low-level classes vs high-level classes.
      
  
## Functional Patterns

- Using expressions rather than statements lets us eliminate unneeded variables