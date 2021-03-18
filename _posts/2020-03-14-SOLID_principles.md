---
title: "5 Principles to write SOLID code"
excerpt: "The SOLID design principles explained with examples in Python."
categories:
  - Blog
tags:
  - design pattern
  - coding
permalink: /solid_principles
published: false
---

As someone who recently started working as a Software Engineer with no formal Computer Science background, I have struggled a lot with coming up with sensible low-level designs and structuring code the right way. Initially, it helped me a lot to be introduced to a checklist of 5 principles to follow, which I will share with you in this post.

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
Instead, I should create a class for interacting with the Albums database. This could be expanded with searching albums by starting letter, number of tracks, etc. (see next principle on how exaclty)

```python
# instead:
class AlbumBrowser:
    """ Class for browsing the Albums database"""
    def search_album_by_artist(self, albums, artist):
        pass
    
    def search_album_starting_with_letter(self, albums, letter):
        pass
 ```

One caveat: Making classes overly simple is making the code just as hard to read, as one would have to follow a long chain of objects passed to one another, and could lead to a fragmented codebase with single-method classes. This principle does not mean that every class should do *one single thing* as in one method, but *one concept*.

### 2.Open-Closed Principle

>Software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification.

This means that I should be able to add new functionality without changing my existing code structure, but adding new code instead. The goal is to change existing, tested code as little as possible to prevent bugs and having to test everything all over again. If this principle is not followed, the result could be a long list of changes in depending classes, regression on existing features, and unnecessary hours of testing.

This is demonstrated by the following example:

```python
class Album:
    def __init__(self, name, artist, songs, genre) -> None:
        self.name = name
        self.artist = artist
        self.songs = songs
        self.genre = genre

#before
class AlbumBrowser:
    def search_album_by_artist(self, albums, artist):
        return [album for album in albums if album.artist == artist]

    def search_album_by_genre(self, albums, genre):
        return [album for album in albums if album.genre == genre]

```

Now what happens if I want to search *by artist and genre*? What if I add *release year*? I will have to write new function every time (in total 2^n-1 to be precise), and the number grows exponentially.

Instead, I should define a base class with common interface for my specification with the methods I want as a class, and then define subclasses for each type of specification that inherit these interfaces from the base class:

```python
#after
class SearchBy:
    def is_matched(self, album):
        pass
      
class SearchByGenre(SearchBy):
    def is_matched(self, album, genre):
        return album.genre == genre
    
class SearchByArtist(SearchBy):
    def is_matched(self, album, artist):
        return album.genre == artist
    
class AlbumBrowser:
    def browse(self, albums, searchby):
        return [album for album in albums if searchby.is_matched(album)]
    
```

This allows us to extend the searches with another class when we want (e.g. by release date). Any new search class will need to satisfy the interface defined by Searchby, so we won't have surprises when interacting with our existing code. To browse by a criteria, we now need to create a SearchBy object first and pass that into AlbumBrowser.  

But what about multiple criteria? I really like this solution I saw in this [Design Patterns Udemy Course](https://www.udemy.com/course/design-patterns-python/). This allows use to join browsing criterias to be joined together by `&`:

```python
#add __and__:
class SearchBy:
    def is_matched(self, album):
        pass
    
    def __and__(self, other):
        return AndSearchBy(self, other)

class AndSearchBy(SearchBy):
    def __init__(self, searchby1, searchby2):
        self.searchby1 = searchby1
        self.searchby2 = searchby2

    def is_matched(self, album):
        return self.searchby1.is_matched(album) and self.searchby2.is_matched(album)
```

This `&` method can be a bit confusing, so the following example demonstrates the usage:

```python
LAWoman = Album(
    name="L.A. Woman",
    artist="The Doors",
    songs=["Riders on the Storm"],
    genre="Rock",
)

Trash = Album(
    name="Trash",
    artist="Alice Cooper",
    songs=["Poison"],
    genre="Rock",
)
albums = [LAWoman, Trash]

# this creates the AndSearchBy object
my_search_criteria = SearchByGenre(genre="Rock") & SearchByArtist(
    artist="The Doors"
)

browser = AlbumBrowser()
assert browser.browse(albums=albums, searchby=my_search_criteria) == [LAWoman]
# yay we found our album
```

### 3. Liskov Substituion Principle

