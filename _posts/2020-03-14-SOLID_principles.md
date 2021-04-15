---
title: "5 Principles to write SOLID Code"
excerpt: "The SOLID design principles explained with examples in Python."
categories:
  - Blog
tags:
  - design pattern
  - coding
permalink: /solid_principles
published: true
comments: true

---

As someone who recently started working as a Software Engineer with no formal Computer Science background, I have struggled a lot with coming up with sensible low-level designs and structuring code the right way. Initially, it helped me a lot to think of a checklist of 5 principles to follow, which I will share with you in this post.

### SOLID Design Principles

SOLID is the acronym for a collection of 5 object-oriented design principles, first conceptualised by Robert C. Martin about 20 years ago, and they have shaped the way we write software today.

<figure>
	<a<img src="http://alexandrasouly.github.io/images/thisisengineering-raeng-64YrPKiguAE-unsplash.jpg"></a>
	<figcaption><a title="Photo by ThisisEngineering RAEng on Unsplash">Photo by ThisisEngineering RAEng on Unsplash</a>.</figcaption>
</figure>

They are meant to help creating simpler, more easily understandable, maintainable and expandable code. This becomes essential when a large group of people is working on codebases that are always growing and evolving, often made up of hundreds of thousands (if not millions) of lines of code. The principles are signposting the way to maintaining good practice, and writing better quality code.

The letters stand for:

1. **S**ingle Responsiblity Principle
2. **O**pen/Closed Principle
3. **L**iskov Substitution Principle
4. **I**nterface Segregation Principle
5. **D**ependency Inversion Principle

They are all simple concepts that are easy to grasp, but really valuable when writing industry-standard code.

### 1. Single Responsiblity Principle

>A class should have one, and only one, reason to change.

This is probably the most intuitive principle, also true for software components or microservices. Having “only one reason to change” could be restated as having “only one responsibility”. This makes code more robust and flexible, easier to understand for someone else, and you will avoid some unexpected side-effects when changing existing code. You will also need to make fewer changes: the more independent reasons a class has to change, the more often it has to change. If you have lots of classes depending on each other, the number of changes you need to make might grow exponentially. The more complicated your classes are, the more difficult it is to change them without unexpected consequences.

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
In the above example, I have created a class `Album`. This stores the album name, artist and tracklist, and can manipulate the contents of the album, such as adding songs or deleting. Now, if I add a function to search albums from the same artist, I break the Single Responsibility Principle. My class would have to change if I decide to store albums in a different way (for example by adding the record label or storing the tracklist as a dictionary of track name and length), and my class would also need to change if I change the database where I store these albums ( for example I move to an online database from an Excel sheet). It is clear that these are two distinct responsabilities.  
Instead, I should create a class for interacting with the Albums database. This could be expanded with searching albums by starting letter, number of tracks, etc. (see next principle on how exactly)

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

This means that I should be able to add new functionality without changing my existing code structure, but by adding new code instead. The goal is to change existing, tested code as little as possible to prevent bugs and having to test everything all over again. If this principle is not followed, the result could be a long list of changes in depending classes, regression on existing features, and unnecessary hours of testing.

This is demonstrated by the following example:

```python
class Album:
    def __init__(self, name, artist, songs, genre):
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

Now what happens if I want to search *by artist and genre*? What if I add *release year*? I will have to write new function every time (in total $$2^n-1$$ to be precise), and the number grows exponentially.

Instead, I should define a base class with a common interface for my specification, and then define subclasses for each type of specification that inherits ths interface from the base class:

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

This principle is by Barbara Liskov, who formulated her principle very formally:
>“Let φ(x) be a property provable about objects x of type T. Then φ(y) should be true for objects y of type S where S is a subtype of T.”

This means that if we have a base class T and subclass S, you should be able to substitute the main class T with the subclass S without breaking the code. The interface of a subclass should be the same as the interface of the base class, and the subclass should behave in the same way as the base class.

If you have a method in T that is being overridden in S, then both methods should take the same inputs, and return the same type of output. The subclass can return only a subset of the return values of the base class, but it should accept all the inputs the base class does.

In the classic example with rectangles and squares, we create a Rectangle class, with width and height setters. If you have a square, the width setter also needs to resize the height, and vice versa to keep the square property. This forces us to make a choice: we either keep the implementation of the Rectangle class, but then Square stops being a square when you use the setter on it, or you change the setters to make height and width the same for squares. This could lead to some unexpected behaviour if you have a function that resizes the height of your shape. 

```python
class Rectangle:
    def __init__(self, height, width):
        self._height = height
        self._width = width

    @property
    def width(self):
        return self._width

    @width.setter
    def width(self, value):
        self._width = value

    @property
    def height(self):
        return self._height

    @height.setter
    def height(self, value):
        self._height = value

    def get_area(self):
        return self._width * self._height


class Square(Rectangle):
    def __init__(self, size):
        Rectangle.__init__(self, size, size)

    @Rectangle.width.setter
    def width(self, value):
        self._width = value
        self._height = value

    @Rectangle.height.setter
    def height(self, value):
        self._width = value
        self._height = value


def get_squashed_height_area(Rectangle):
    Rectangle.height = 1
    area = Rectangle.get_area()
    return area


rectangle = Rectangle(5, 5)
square = Square(5)

assert get_squashed_height_area(rectangle) == 5  # expected 5
assert get_squashed_height_area(square) == 1  # expected 5
```

While this might not seem a big deal (surely you can just remember sqaure changes the width too?!), this becomes a bigger issue when the functions are more complicated or when you are using some else's code, and just assume the subclass behaves the same.

A short, but intuitive example I really like from the [Circle-ellipse problem Wiki article](https://en.wikipedia.org/wiki/Circle%E2%80%93ellipse_problem#Description):

```python 
class Person():
    def walkNorth(meters):
        pass
    def walkSouth(meters):
        pass

class Prisoner(Person):
    def walkNorth(meters):
        pass 
    def walkSouth(meters):
        pass
```
Obvisouly, we cannot implement the walk methods on prisoners, as they are not free to walk arbitrary distances in arbitrary directions. We shouldn't be allowed to call walk methods on the class, the interface is wrong. Which leads us to our next principle...

### 4. Interface Segregation Principle

> “Clients should not be forced to depend upon interfaces that they do not use.”

If you have a base class with many methods, possibly not all of your subclasses are going to need them, maybe just a few. But due to inheritance, you will be able to call these methods on all the subclasses, even on those that don't need it. This means a lot of interfaces that are unused, unneeded and will result in bugs when they get accidentally called.  

This principle is meant to prevent this from happening. We should make interfaces as small as possible, so that we don't need to implement functions we don't need. Instead of one big base class, we should split them into multiple ones. They should only have methods that make sense for each, and then have our subclasses inherit from them.

In the next example, we wil be using abstract methods. Abstract methods create an interface in a base class that have no implementation, but are forced to be implemented in every subclass that inherits from the base class. Abstract methods are essentially enforcing an interface.

```python
class PlaySongs:
    def __init__(self, title):
        self.title = title

    def play_drums(self):
        print("Ba-dum ts")

    def play_guitar(self):
        print("*Soul-moving guitar solo*")

    def sing_lyrics(self):
        print("NaNaNaNa")

# This class is fine, just changing the guitar and lyrics
class PlayRockSongs(PlaySongs): 
    def play_guitar(self):
        print("*Very metal guitar solo*")

    def sing_lyrics(self):
        print("I wanna rock and roll all night")

# This breaks the ISP, we don't have lyrics 
class PlayInstrumentalSongs(PlaySongs):
    def sing_lyrics(self):
        raise Exception("No lyrics for instrumental songs")

```
Instead, we could have a class for the singing and the music separately (assuming guitar and drums always happen together in our case, otherwise we need to split them up even more, perhaps by instrument.)
This way, we only have the interfaces we need, we cannot call sing lyrics on instrumental songs.

```python
from abc import ABCMeta


class PlaySongsLyrics:
    @abstractmethod
    def sing_lyrics(self, title):
        pass


class PlaySongsMusic:
    @abstractmethod
    def play_guitar(self, title):
        pass

    @abstractmethod
    def play_drums(self, title):
        pass


class PlayInstrumentalSong(PlaySongsMusic):
    def play_drums(self, title):
        print("Ba-dum ts")

    def play_guitar(self, title):
        print("*Soul-moving guitar solo*")


class PlayRockSong(PlaySongsMusic, PlaySongsLyrics):
    def play_guitar(self):
        print("*Very metal guitar solo*")

    def sing_lyrics(self):
        print("I wanna rock and roll all night")

    def play_drums(self, title):
        print("Ba-dum ts")

```
### 5. Dependency Inversion Principle

The last principle says
> 1. High-level modules should not depend on low-level modules. Both should depend on abstractions (e.g. interfaces).
> 2. Abstractions should not depend on details. Details (concrete implementations) should depend on abstractions.

If your code has well-defined abstract interfaces, changing the internal implementation of one class shouldn't break your code. A class it interacts with should not have knowledge of the inner workings of the other class, and should be unaffected as long as the interfaces are the same. An example would be changing the type of database you use (SQL or NoSQL) or changing the data structure you store your data in (dictionary or list).

This is illustrated in the following example, where ViewRockAlbums explicitly depends on the fact that albums are stored in a tuple in a certain order inside AlbumStore. It should have no knowledge of the internal structure of Albumstore. Now if we change the ordering in the tuples in the album, our code would break.

```python
class AlbumStore:
    albums = []

    def add_album(self, name, artist, genre):
        self.albums.append((name, artist, genre))


class ViewRockAlbums:
    def __init__(self, album_store):
        for album in album_store:
            if album[2] == "Rock":
                print(f"We have {album[0]} in store.")
```

Instead, we need to add an abstract interface to AlbumStore to hide the details, that can be called by other classes. This should be done as in the example in the Open-Closed Principle, but assuming we don't care about filtering by anything else, I'll just add a filter_by_genre method. Now if we had another type of AlbumStore, that decides to store the album differently, it would need to implement the same interface for filter_by_genre to make ViewRockAlbums work.

```python
class GeneralAlbumStore:
    @abstractmethod
    def filter_by_genre(self, genre)
        pass

class MyAlbumStore(GeneralAlbumStore):
    albums = []

    def add_album(self, name, artist, genre):
        self.albums.append((name, artist, genre))

    def filter_by_genre(self, genre):
        if album[2] == "Rock":
            yield album[0]

class ViewRockAlbums:
    def __init__(self, album_store):
        for album_name in album_store.filter_by_genre("Rock"):
            print(f"We have {album_name} in store.")
```

### Conclusion

The SOLID design principles are meant to be a guideline to write maintainable, expandable and easy to understand code. It is worth keeping them in mind next time you think of a design, to write SOLID code.