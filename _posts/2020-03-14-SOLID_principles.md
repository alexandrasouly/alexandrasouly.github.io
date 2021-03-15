---
title: "5 Principles to write SOLID code"
excerpt: "The SOLID design principles explained with examples in Python."
categories:
  - Blog
tags:
  - design pattern
  - coding
permalink: /solid_principles
---

As someone who recently started working as a Software Engineer with no formal Computer Science background, I have struggled a lot with coming up with sensible low-level designs and structuring code the right way. A lot of my struggles would have been helped by having a checklist of 5 principles to follow, which I will share with you in this post.

### SOLID Design Principles

SOLID is the acronym for the collection of 5 object-oriented design principles first conceptualised by Robert C. Martin about 20 years ago, and they have shaped the way we write software today.

They are principles meant to help creating simpler, more easily understandable, maintainable and expandable code. This becomes essential when a large group of people is working on codebases that are always growing and evolving, often made up of  houndreds of thousands of lines of code. They are signposting the way to maintaining good practice, and writing better quality code.

The letters stand for:

1. **S**ingle Responsiblity Principle
2. **O**pen/Closed Principle
3. **L**iskov Substitution Principle
4. **I**nterface Segregation Principle
5. **D**ependency Inversion Principle

They are all simple concepts that are easy to grasp, but really valuable when writing industry-standard code. I will now explain all five of them, with examples in Python.

### 1. Single Responsiblity Principle

>A class should have one, and only one, reason to change.

This is probably the most intuitive principle. This states that classes should have only reason to change, which can be restated as it should have one responsibility. This is also true for software components or microservices. This makes your code more robust and flexible, easier to understand for someone else and you will avoid some unexpected side-effects when changing existing code. You will also need to make fewer changes: the more independent reasons a class has to change, the more often it has to change. If you have lots of classes depending on each other, the number of changes you need to make might grow exponentially. The more complicated your classes are, the more difficult it is to change them without unexpected consequences.

```python
class Menu:
    def __init__(self) -> None:
        self.food = {}

    def add_food(self, name, price):
        self.food[name] = price

    def remove_food(self, name):
        self.food.pop(name)

    def __str__(self) -> str:
        items = [f"{food}: £{price}" for food, price in self.food.items()]
        return "\n".join(items)

    # breaks the SRP
    def save_to_file(self, filename):
        f = open(filename, "w")
        f.write(str(self))
        f.close()
```
In the above example, I have created a clall `menu`

```python
# instead:
class PersistenceManager:
    def save(menu, filename):
        f = open(filename, "w")
        f.write(str(menu))
        f.close()
 ```