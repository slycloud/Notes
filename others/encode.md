## Encoding

### Concept

#### Code Point Count

### Encode standard

#### Unicode
固定字符数表示，所有字符都用2bytes表示，包括英文字母，汉字，特殊字符等。

#### UTF-8
可变字符数表示，如英文字母为1byte，汉字为3bytes。

#### UTF-16

### Encode in JVM
> Java uses UTF-16 for the internal text representation.
> How is text represented in the Java platform?
>
> The Java programming language is based on the Unicode character set, and several libraries implement the Unicode standard. The primitive data type char in the Java programming language is an unsigned 16-bit integer that can represent a Unicode code point in the range U+0000 to U+FFFF, or the code units of UTF-16. The various types and classes in the Java platform that represent character sequences - char[], implementations of java.lang.CharSequence (such as the String class), and implementations of java.text.CharacterIterator - are UTF-16 sequences.

Java内部的String是由UTF-16表示，如果使UseCompressedOops有效时，部分不需要16bit来表示的字符将会被压缩。
