<!-- ---
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
class Album:
    def __init__(self, name, artist, songs) -> None:
        self.name = name
        self.artist = artist
        self.songs = songs

    def add_song(self, song):
        self.songs.append(song)

    def remove_song(self, song):
        self.songs.remove(song) 

    def __str__(self) -> str:
        return f"Album {self.name} by {self.artist}\nTracklist:\n{self.songs}"

    # breaks the SRP
    def search_album_by_artist(self):
        """ Searching the database for other albums by same artist """
        pass
```
In the above example, I have created a class `Album`. This stores the album name, artist and tracklist, and can manipulate the contents of the album, such as adding songs or deleting. Now, if I add a function to search albums from the same artist, I break the Single Responsibility Principle. My class would have to change if I decide to store albums in a different way (for example by adding the record label or storing the traclist as a dictionary of track name and length) and my class would also need to change if I change the database where I store these albums ( for example I move to an online database from an excel sheet). It is clear that these are two distinct responsablities.  
Instead, I should create a class for interacting with the Albums database. This could be expanded with searching albums by starting letter, number of tracks, etc.

```python
# instead:
class AlbumBrowser:
    """ Class for browsing the Albums database"""
    def search_album_by_artist(self, Album):
        pass
    
    def search_album_starting_with_letter(self, letter):
        pass
 ```

One caveat: Making classes overly simple is making the code just as hard to read, as one would have to follow a long chain of objects passed to one another, and could lead to a fragmented codebase with single-method classes. This principle does not mean that every class should do *one single thing* as in one method, but *one concept*. In less trivial cases, with real-life code one must use good judgement   -->