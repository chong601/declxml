# declxml - Declarative XML Processing
[![PyPI version](https://badge.fury.io/py/declxml.svg)](https://badge.fury.io/py/declxml)
[![Build Status](https://travis-ci.org/gatkin/declxml.svg?branch=master)](https://travis-ci.org/gatkin/declxml)
[![Build status](https://ci.appveyor.com/api/projects/status/00dndldkr0k2if5f/branch/master?svg=true)](https://ci.appveyor.com/project/gatkin/declxml/branch/master)
[![codecov](https://codecov.io/gh/gatkin/declxml/branch/master/graph/badge.svg)](https://codecov.io/gh/gatkin/declxml)
[![Documentation Status](https://readthedocs.org/projects/declxml/badge/?version=latest)](https://declxml.readthedocs.io/en/latest/?badge=latest)
[![License](https://img.shields.io/github/license/mashape/apistatus.svg)](https://pypi.python.org/pypi/declxml/)

XML processing made easy. No more writing and maintaining dozens or hundreds of lines of imperative serialization and parsing logic. With declxml, you declaratively define the structure of your XML document and let declxml handle the rest.

## Installation
```
pip install -U declxml
```

## Documentation
For detailed documentation, see the project's [documentation page](http://declxml.readthedocs.io/).

## Usage
Given some XML to process
```xml
<author>
    <name>Robert A. Heinlein</name>
    <birth-year>1907</birth-year>
    <book>
        <title>Starship Troopers</title>
        <published>1959</published>
    </book>
    <book>
        <title>Stranger in a Strange Land</title>
        <published>1961</published>
    </book>
</author>
```

Create a declxml processor that defines the structure of the document
```python
>>> import declxml as xml

>>> author_processor = xml.dictionary('author', [
...     xml.string('name'),
...     xml.integer('birth-year'),
...     xml.array(xml.dictionary('book', [
...         xml.string('title'),
...         xml.integer('published')
...     ]), alias='books')
... ])
>>>
```

Then, use that processor to parse XML data
```python
>>> author_xml = """
... <author>
...     <name>Robert A. Heinlein</name>
...     <birth-year>1907</birth-year>
...     <book>
...         <title>Starship Troopers</title>
...         <published>1959</published>
...     </book>
...     <book>
...         <title>Stranger in a Strange Land</title>
...         <published>1961</published>
...     </book>
... </author>
... """
>>> xml.parse_from_string(author_processor, author_xml)
{'name': 'Robert A. Heinlein', 'birth-year': 1907, 'books': [{'title': 'Starship Troopers', 'published': 1959}, {'title': 'Stranger in a Strange Land', 'published': 1961}]}
>>>
```

The same processor can also be used to serialize data to XML
```python

>>> author = {
...     'birth-year': 1920,
...     'name': 'Issac Asimov',
...     'books': [
...         {
...             'title': 'I, Robot',
...             'published': 1950
...         },
...         {
...             'title': 'Foundation',
...             'published': 1951
...         }
...     ]
...  }

>>> print(xml.serialize_to_string(author_processor, author, indent='    '))
<?xml version="1.0" encoding="utf-8"?>
<author>
    <name>Issac Asimov</name>
    <birth-year>1920</birth-year>
    <book>
        <title>I, Robot</title>
        <published>1950</published>
    </book>
    <book>
        <title>Foundation</title>
        <published>1951</published>
    </book>
</author>
>>>
```

Want to work with objects instead of dictionaries? You can do that with declxml too.
```python
>>> class Author:
... 
...     def __init__(self):
...         self.name = None
...         self.birth_year = None
...         self.books = []
... 
...     def __repr__(self):
...         return 'Author(name=\'{}\', birth_year={}, books={})'.format(
...             self.name, self.birth_year, self.books)
 
>>> class Book:
... 
...     def __init__(self):
...         self.title = None
...         self.published = None
... 
...     def __repr__(self):
...         return 'Book(title=\'{}\', published={})'.format(self.title, self.published)
...

>>> author_processor = xml.user_object('author', Author, [
...     xml.string('name'),
...     xml.integer('birth-year', alias='birth_year'),
...     xml.array(xml.user_object('book', Book, [
...         xml.string('title'),
...         xml.integer('published')
...     ]), alias='books')
... ])

>>> xml.parse_from_string(author_processor, author_xml)
Author(name='Robert A. Heinlein', birth_year=1907, books=[Book(title='Starship Troopers', published=1959), Book(title='Stranger in a Strange Land', published=1961)])
>>>
```

What about namedtuples, you say? Those are extremely useful, and declxml lets you work with them as well
```python
>>> from collections import namedtuple


>>> Author = namedtuple('Author', ['name', 'birth_year', 'books'])
>>> Book = namedtuple('Book', ['title', 'published'])


>>> author_processor = xml.named_tuple('author', Author, [
...     xml.string('name'),
...     xml.integer('birth-year', alias='birth_year'),
...     xml.array(xml.named_tuple('book', Book, [
...         xml.string('title'),
...         xml.integer('published')
...     ]), alias='books')
... ])

>>> xml.parse_from_string(author_processor, author_xml)
Author(name='Robert A. Heinlein', birth_year=1907, books=[Book(title='Starship Troopers', published=1959), Book(title='Stranger in a Strange Land', published=1961)])
>>>
```
