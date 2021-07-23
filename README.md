# declxml - Declarative XML Processing

This is a fork of [declxml](https://github.com/gatkin/declxml) that aims to implement a few extra features that are unavailable in the original `declxml` library.

# Why?
I like `declxml`, but from my experience working with my [rpm-package-explorer](https://github.com/chong601/rpm-package-explorer) project, it has some issues
- Limited parsing capabilities (XML parsing is only done using `parse_from_file()` or `parse_from_string()`)
- It doesn't expose a few parsing functions that would allow customized parsing logic namely [`parse_at_element()`](https://github.com/gatkin/declxml/blob/8cc2ff2fa813aa9d4c27d1964fe7d865029a1298/declxml.py#L213), [`parse_from_element()`](https://github.com/gatkin/declxml/blob/8cc2ff2fa813aa9d4c27d1964fe7d865029a1298/declxml.py#L222) and [`parse_at_root()`](https://github.com/gatkin/declxml/blob/8cc2ff2fa813aa9d4c27d1964fe7d865029a1298/declxml.py#L254) which would be able to extend the parsing capabilities of `declxml`
