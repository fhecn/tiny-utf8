# TINY <img src="https://github.com/DuffsDevice/tiny-utf8/raw/master/UTF8.png" width="47" height="47" align="top" alt="UTF8 Art" style="display:inline;"> Version 4.a

[![Build Status](https://travis-ci.org/DuffsDevice/tiny-utf8.svg?branch=master)](https://travis-ci.org/DuffsDevice/tiny-utf8)&nbsp;&nbsp;[![Licence](https://img.shields.io/badge/licence-BSD--3-e20000.svg)](https://github.com/DuffsDevice/tiny-utf8/blob/master/LICENCE)

### DESCRIPTION
**Tiny-utf8** is a library for extremely easy integration of Unicode into an arbitrary C++11 project.
The library consists solely of the class `utf8_string`, which acts as a drop-in replacement for `std::string`.
Its implementation is successfully in the middle between small memory footprint and fast access. All functionality of `std::string` is therefore replaced by the corresponding codepoint-based UTF-32 version - translating every access to UTF-8 under the hood.

#### *CHANGES BETWEEN Version 4.a and 3.2.4*

- **Class `utf8_string` is now defined inside `namespace tiny_utf8`**. If you want the old declaration in the global namespace, `#define TINY_UTF8_GLOBAL_NAMESPACE`
- ***NEW: Support for C++20***: Use class `tiny_utf8::u8string`, which uses `char8_t` as underlying data type (instead of `char`)

#### FEATURES
- **Drop-in replacement for std::string**
- **Lightweight and self-contained** (~3K SLOC)
- **Very fast**, i.e. highly optimized decoder, encoder and traversal routines
- **Advanced Memory Layout**, i.e. Random Access is
   - ***O(1) for ASCII-only strings (!)*** and
   - O("#Codepoints > 127") for the average case.
   - O(n) for strings with a high amount of non-ASCII code points
- **Small String Optimization** (SSO) for strings up to an UTF8-encoded length of `sizeof(utf8_string)`! That is, including the trailing `\0`
- **Growth in Constant Time** (Amortized)
- **On-the-fly Conversion between UTF32 and UTF8**
- Small Stack Size, i.e. `sizeof(utf8_string)` = 16 Bytes (32Bit) / 32 Bytes (64Bit)
- Codepoint Range of `0x0` - `0xFFFFFFFF`, i.e. 1-7 Code Units/Bytes per Codepoint (Note: This is more than specified by UTF8, but until now otherwise considered out of scope)
- Single Header File
- Straightforward C++11 Design
- Possibility to prepend the UTF8 BOM (Byte Order Mark) to any string when converting it to an std::string
- Supports raw (Byte-based) access for occasions where Speed is needed
- Supports `shrink_to_fit()`
- Malformed UTF8 sequences will **lead to defined behaviour**

## THE PURPOSE OF TINY-UTF8
Back when I decided to write a UTF8 solution for C++, I knew I wanted a drop-in replacement for `std::string`. At the time mostly because I found it neat to have one and felt C++ always lacked accessible support for UTF8. Since then, several years have passed and the situation has not improved much. That said, things currently look like they are about to improve - but that doesn't say much, does it?

The opinion shared by many "experienced Unicode programmers" (e.g. published on [UTF-8 Everywhere](utf8everywhere.org)) is that "non-experienced" programmers both *under* and *over*estimate the need for Unicode- and encoding-specific treatment: This need is...
  1. **overestimated**, because many times we really should care less about codepoint/grapheme borders within string data;
  2. **underestimated**, because if we really want to "support" unicode, we need to think about *normalizations*, *visual character comparisons*, *reserved codepoint values*, *illegal code unit sequences* and so on and so forth.

Unicode is not rocket science but nonetheless hard to get *right*. **Tiny-utf8** does not intend to be an enterprise solution like [ICU](http://site.icu-project.org/) for C++. The goal of **tiny-utf8** is to
  - bridge as many gaps to "supporting Unicode" as possible by 'just' replacing `std::string` with a custom class which means to
  - provide you with a Codepoint Abstraction Layer that takes care of the Run-Length Encoding, without you noticing.

**Tiny-utf8** aims to be the simple-and-dependable groundwork which you build Unicode infrastructure upon. And, if *1)* C++2a should happen to make your Unicode life easier than **tiny-utf8** or *2)* you decide to go enterprise, you have not wasted much time replacing `std::string` with `utf8_string` either. This is what makes **tiny-utf8** so agreeable.

#### WHAT TINY-UTF8 IS NOT AIMED AT
- Conversion between ISO encodings and UTF8
- Interfacing with UTF16
- Visible character comparison (`'ch'` vs. `'c'+'h'`)
- Codepoint Normalization
- Correction of invalid Code Unit sequences
- Detection of Grapheme Clusters

Note: ANSI suppport was dropped in Version 2.0 in favor of execution speed.

## EXAMPLE USAGE

```cpp
#include <iostream>
#include <algorithm>
#include <tinyutf8.h>
using namespace std;
using namespace tiny_utf8;

int main()
{
    utf8_string str = u8"!🌍 olleH";
    for_each( str.rbegin() , str.rend() , []( char32_t codepoint ){
      cout << codepoint;
    } );
    return 0;
}
```

## BUILD MODES

Since Version 3, **tiny-utf8** is header-only, i.e. *tinyutf8.h* includes all definitions and one doesn't need to build *tinyutf8.cpp* separately. If you want to speed up your compilation time with a large number of source files, include *tinyutf8.hpp* instead, which `#define`s `TINY_UTF8_FORWARD_DECLARE_ONLY` and then includes *tinyutf8.h*, which now has all definitions removed.

Subsequently, in order to build the definitions in *tinyutf8.h* in a separate source file, add *tinyutf8.cpp* to your source files, which only includes *tinyutf8.h* (instead of *tinyutf8.h**pp***).

#### EXCEPTIONS

- **Tiny-utf8** should automaticall detect, whether your build system allows the use of exceptions or not. This is done by checking for the feature test macro `__cpp_exceptions`.
- If you would like **tiny-utf8** to be `noexcept` anyway, `#define` the macro `TINY_UTF8_NOEXCEPT`.
- If you would like **tiny-utf8** to use a different exception strategy, `#define` the macro `TINY_UTF8_THROW( location , failing_predicate )`. For using assertions, you would write ``#define TINY_UTF8_THROW( _ , pred ) assert( pred ).
- *Hint:* If exceptions are disabled, `TINY_UTF8_THROW( ... )` is automatically defined as `void()`. This works well, because all uses of `TINY_UTF8_THROW` are immediately followed by a corresponding `return` statement.

#### BACKWARDS-COMPATIBLE BUILD

If you would like to stay compatible with 3.2.* and have `utf8_string` defined in the global namespace, `#define` the macro `TINY_UTF8_GLOBAL_NAMESPACE`.

## BUGS

If you encounter any bugs, please file a bug report through the "Issues" tab.
I'll try to answer it soon!

Jakob
