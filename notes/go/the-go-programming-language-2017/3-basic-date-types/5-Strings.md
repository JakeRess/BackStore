# Strings

- a string is an immutable sequence of `bytes`
- text strings are conventionally interpreted as `UTF-8-encoded` sequence of `Unicode code points (runes)`
- `len` returns the number of bytes (not runes) in a string.
- and the index `s[i]` retrieves the i-th byte of string s.

- the `i-th` byte of a string is not necessarily the i-th character of a string.
- because the UTF-8 encoding of a non-ASCII code point requires two or more bytes.

## substring

- `s[i:j]` yields a new string consisting of the bytes of the original string straring at index i, and not including index j
- either or both of the `i` and `j` operands may be omitted.
- 0 and len(s) are the default values.

- `+` makes a new string

## compare string

- strings may be compared with `==` and `<`
- the comparion is done byte to byte
- so the result is the natural lexicographic ordering.

## immutable

- string values are immutable
- the byte sequence contained in a string value can never be changed
- though of course, we can assign a new value to a string variable
- try to midfy a string's data in place are not allowed
- `s[0] = 'L'` compile error

> immutability means that it is safe for two copies of a string to share underlying memory
> making it cheap to copy strings of any length

> similarly, a string `s` and a substring like `s[7:]`
> may safely share the same data.
> so the underlying substring operation is also cheap

> no new memory is allocated in either case.

## string literals

- because Go source file are always encoded in UTF-8
- and Go text strings are conventionally interpreted as UTF-8
- we can include Unicode code points in string literals

### escape sequence

- within a double-quoted string literal, `escape sequence` that begin with `\` can be used 
- to insert arbitrary byte values into the string.

- one set of escapes handles ASCII control codes like newline, carrige return and tab:
1. \a alert or bell
2. \b backspace
3. \f form feed 换页
4. \n newline
5. \r carriage return 
6. \t tab
7. \v vertical tab
8. \' singel quote (only in the rune literal '\'')
9. \" double quote (only within "..." literal)
10. \\ backslash

- arbitrary bytes can also be included in literal strings using `hexadecimal` or `octal escapes`
- a hexadecimal escape is written `\xhh`, with exactly two hexadecimal digits `h` (in upper or lower case)
- a octal escape is written `\ooo` with exactly three octal digits `o` not exceeding `\377`

### raw string literal

- a raw string literal is written `....` 
- within a raw string literal, no escape sequence are processed.
- so a raw string literal may spread over several lines in the program source.
- The only processing is that carriage returns are deleted so that the value of the string is the same on all platforms,
- including those that conventionally put carriage returns in text files.

- raw string literals are a convenient way to write `regular expressions`
- which tend to have lots of backslashes.

- they are also useful for HTML templates, JSON literals, command usage messages, and the like which often extends over multiple lines.

```go
const GoUsage = `Go is a tool for managing Go source code.
Usage:
    go command [arguments]
`
```

## Unicode

### ASCII

- long ago, only one character set to deal with: `ASCII`, the American Standard Code for Information Interchange
- `ASCII` or more precisely `US-ASCII`
- use `7` bits to represent `128` 
- the upper- and lower-case letters of English, digits, and a variety of punctuation and device-control characters. 

- With the growth of the Internet, data in myriad languages has become much more common.

### Unicode
- collects all of the characters in all of the world's writing system
- assign each one a standard number called a `Unicode code point` or in Go, a `rune`

- Unicode version 8 defines code points for over 120,000 characters in well over 100 languages and scripts.

- the natural data type to hold a `single rune` is `int32` and that's what Go uses.

- we could represent a sequence of runes as a sequence of `int32` values.
- in this representation, which is called `UTF-32` or `UCS-4`
- the encoding of each Unicode code point has the same size, 32 bits.
- this is simple and uniform, but it uses much more space than neccessary
- since most computer-readable text is in ASCII
- which requires only 8 bits or 1 byte per character

-  All the characters in widespread use still number fewer than 65,536, which would fit in 16 bits

### UTF-8

- UTF-8 is a variable-length encoding of Unicode code points as bytes. 
- UTF-8 was invented by Ken Thompson and Rob Pike, two of the creators of Go, and is now a Unicode standard.
- it uses between `1` and `4` bytes to represent each rune.
- but only `1 byte` for ASCII characters
- only 2 or 3 bytes for most runes in common use.
- the high order bits of the first byte of the encoding for a rune indicate how many bytes follow.

- A high-order `0` indicates 7-bit ASCII, where each rune takes only `1` byte
- a high-order `110` indicates that the rune takes 2 bytes

- A variable-length encoding precludes direct indexing to access the n-th character of a string,
- but UTF-8 has many desirable properties to compensate.

- Go source files are always encoded in UTF-8, and UTF-8 is the preferred encoding for text strings manipulated by Go programs.
- the `unicode` package provides functions for working with individual runes.
- the `unicode/utf8` package provides functions for encoding and decoing runes as bytes using UTF-8

### Unicode escapes

- Many Unicode characters are hard to type on a keyboard or to distinguish visually from sim- ilar-looking ones; some are even invisible.
- Unicode escapes in Go string literals allow us to specify them by their numeric code point value. 
- There are two forms, 
1. \uhhhh for a 16-bit value
2. \Uhhhhhhhh for a 32-bit value.
- where each h is a hexadecimal digit; 

- Each denotes the UTF-8 encoding of the specified code point.
```go
"BF" 
"\xe4\xb8\x96\xe7\x95\x8c" 
"\u4e16\u754c" 
"\U00004e16\U0000754c"
```

- Unicode escapes may also be used in rune literals.

```go
'B' 
'\u4e16' 
'\U00004e16'
```

- A rune whose value is less than 256 may be written with a single hexadecimal escape, 
- such as '\x41' for 'A', but for higher values, a \u or \U escape must be used.

## string operations

> Thanks to the nice properties of UTF-8, many string operations don’t require decoding. 

```go
func HasPrefix(s, prefix string) bool {
	return len(s) >= len(prefix) && s[:len(prefix)] == prefix
}
func HasSuffix(s, suffix string) bool {
	return len(s) >= len(suffix) && s[len(s)-len(suffix):] == suffix
}
func Contains(s, substr string) bool {
	for i := 0; i < len(s); i++ {
		if (HasPrefix(s[i:], substr)) {
			return true
		}
	}
	return false
}
```

- if we really care about the individual Unicode characters, we have to use other mechanisms.
- s := "Hello, 世界"
: the `s` contains `13 bytes`, but interpreted as `UTF-8`, it encodes only `nine code points or runes`

```go
s := "Hello, 世界"
fmt.Println(len(s)) // 13
fmt.Println(utf8.RuneCountInString(s)) // 9
```
- to process those characters, we need a `UTF-8` decoder

```go
for i := 0; i < len(s); {
    r, size := utf8.DecodeRuneInString(s[i:])
    fmt.Printf("%d\t%c\n", i, r)
    i += size
}
```
- each call to `DecodeRuneInString` returns `r`, the rune itself, and `size`, the number of bytes occupied by the UTF-8 encoding of `r`
- the `size` is used to update the byte index `i` of the next rune in the string.

- Go's `rang loop`, when applied to a string, performs `UTF-8` decoding implicitly

```go
for i, r := range s {
    fmt.Printf("%d\t%q\t%d\n", i, r, r)
}
```

- we could use a simple `range loop` to count the number of `runes` in a string.

```go
n := 0
for range s {
    n++
}
```

- for correct use of range loops on strings, it’s more than a convention, it’s a necessity
- What happens if we range over a string containing arbitrary binary data or, for that matter, UTF-8 data containing errors?
- Each time a UTF-8 decoder, whether explicit in a call to utf8.DecodeRuneInString or implicit in a range loop,
- consumes an unexpected input byte, it generates a special Unicode replacement character, '\uFFFD'
- which is usually printed as a white question mark inside a black hexagonal or diamond-like shape.
- When a program encounters this rune value, it’s often a sign that some upstream part of the system that generated the string data has been careless in its treatment of text encodings.

- UTF-8 is exceptionally convenient as an interchange format but within a program runes may be more convenient
- because they are of `uniform size` and are thus easily indexed in arrays and slices.

- `[]rune` 
-  a `[]rune` conversion applied to a `UTF-8` string returns the sequence of `Unicode code points` that the string encodes

```go
r := []rune(s)
fmt.Printf("%x\n", r)
```
- (The verb % x in the first Printf inserts a space between each pair of hex digits.)
- If a slice of runes is converted to a string, it produces the concatenation of the UTF-8 encodings of each rune:

- converting an `integer value to a string` interprets the integer as a `rune` value
- and yields the UTF-8 representation of that rune.
- If the rune is invalid, the replacement character is substituted:
- fmt.Println(string(1234567)) // �

## Strings and Byte Slices

- four standard packages are particularly important for manipulating strings: `bytes`, `strings`, `strconv`, `unicode`
- the `strings` package provides many functions for searching, replacing, comparing, trimming, splitting, and joining strings.

- because strings are `immutable`, building up strings incrementally can involve a lot of allocation and copying.
- it's more efficient to use the `bytes.Buffer` type

- the `strconv` package provides functions for converting boolean, integer, and floating-point values to and from their string representations
- and functions for `quoting` an `unquoting` strings

- the `unicode` package provides functions like `IsDigit, IsUpper` for `classifying runes`
- conversion functions like `ToUpper` and `ToLower` converts a rune into the given case

- the `strings` package has similar functions, also called `ToUpper` and `ToLower`,
- returns a new string with the specified transformation applied to each character

```go
func basename1(s string) string {
	for i := len(s) - 1; i >= 0; i-- {
		if s[i] == '/' {
			s = s[i+1:]
			break
		}
	}

    // preserve everything before .
	for i := len(s) - 1; i >= 0; i-- {
		if s[i] == '.' {
			s = s[:i]
			break
		}
	}
	return s
}
```
```go
func basename2(s string) string {
	slash := strings.LastIndex(s, "/") // -1 if not found
	s = s[slash+1:]
	if dot := strings.LastIndex(s, "."); dot >= 0 {
		s = s[:dot]
	}
	return s
}
```

- the `path` and `path/filepath` package provides a more general set of functions for manipulated hierarchical names.
- the `path` package works with slash-delimited paths on any platform. but it shouldn't be used for file names.
- `path/filepath` manipulates file names using the rules for the host system.
- such as /foo/bar for POSIX or c:\foo\bar on Microsoft Windows.

```go
func comma(s string) string {
	n := len(s)
	if n <= 3 {
		return s
	}
	return comma(s[:n-3]) + "," + s[n-3:]
}
```

## a byte slice

- the elements of a byte slice can be freely `modified`.

```go
s := "abc"
b := []byte(s)
s2 := string(b)
```
- the `[]byte` conversion allocates a new byte array holding a copy of the bytes of `s`
- and yields a slice that references the entirely of that array.

### Buffer

- the `bytes` package provides the `Buffer` type for efficient manipulation of `byte slices`
- a `Buffer` starts out empty but grows as data of types like `string`, `byte`, `[]byte` are written to it.

```go
func intsToString(values []int) string {
	var buf bytes.Buffer
	buf.WriteByte('[')
	for i, v := range values {
		if (i > 0) {
			buf.WriteString(",")
		}
		fmt.Fprintf(&buf, "%d", v)
	}
	buf.WriteByte(']')
	return buf.String()
}
```

- when appending `UTF-8` encoding of an `rune`, it's best to use `bytes.Buffer's WriteRune`

### Conversions between Strings and Numbers.

- `strconv` package

- convert an integer to string
```go
x := 123
y := fmt.Sprintf("%d", x)
z := strconv.Itoa(x)
fmt.Println(y, z)
```
- `strconv.Itoa` (integer to ASCII)

- format numbers in a different `base`
```go
fmt.Println(strconv.FormatInt(int64(x), 2))
```

- parse a string to an integer

```go
x, err := strconv.Atoi("456")
i, err := strconv.ParseInt("456", 10, 64)
fmt.Println(x, err, i, err)
```
- `ParseInt`
: The third argument of ParseInt gives the size of the integer type that the result must fit into;
: 16 implies int16
: 0 implies int. 
