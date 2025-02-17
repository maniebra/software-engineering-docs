
# Definition

## The "**formal**" definition

> The Observer Pattern defines a one-to-many dependency between objects so that when one object changes state, all of its dependents are notified and updated automatically.

## What it actually means

Suppose you have a waitperson at a restaurant holding an important meeting. This server will make the arrangements and synchronize all the entities (including guests and other wait-people). You can tell them to add something (like a drink) to your bill, and the waitperson will track all the information from all the entities across the restaurant. The observer pattern does the same thing. It keeps track of the data needed globally across the program.


## The meaning of "loose coupling"

When two objects are loosely coupled, they can interact but they have little-to-no knowledge of one another.

## How can loose coupling help us?

Loosely coupled designs allow us to build flexible object oriented systems that can handle change because they minimize the inter-dependency between objects.

# Practical Examples

- The **event listeners** in a web app follow this pattern.
- 