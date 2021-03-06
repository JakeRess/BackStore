> **java.io**

# I/O Basics
> Java's I/O system is cohesive and consistent
> once you understand its fundamentals, the rest of the I/O system is easy to master

## Streams
> Java programs perform I/O through streams
> **a stream is an abstraction that either produces or consumes information**
> a stream is linked to a physical device by the Java I/O system
> all streams behave in the same manner, even if the actual physical devices which they are linked differ
> this means an input stream can abstract many different kinds of input

> in addition to the stream-based I/O defined in **java.io**
> Java also provides **buffer-and channel-based I/O**, which is defined in **java.nio**

### Byte Streams and Character Streams
> Java defines two types of I/O streams: **byte and character**

> **Byte streams**  provide a convenient means for handling input and output of **bytes**
- byte streams are used, for example, when reading or writing binary data

> **Character streams** provide a convenient means for handling input and output of **characters**
- they use **Unicode**, and therefore, can be **internationalized**
- in same cases, character streams are more efficient than byte streams

> as a general rule, old code should be updated to take advantage of character streams where appropriate.

> **at the lowest level, all I/O is still byte-oriented**
> the character-based streams simply provide a convenient and efficient means for handling characters

#### The Byte Stream Classes
> defined using two class hierarchies, at the top are two abstract classes: **InputStream** and **OutputStream**
> to use the stream classes, you must import **java.io**

1. BufferedInputStream
2. BufferedOutputStream
3. ByteArrayInputStream
   : input stream that reads from a byte array
4. ByteArrayOutputStream
   : output stream that writes to a byte array
5. DataInputStream
   : an input stream that contains methods for reading the Java standard data types
6. DataOutputStream
   : an output stream that contains methods for writing the Java standard data types
7. FileInputStream
   : input stream that reads from a file
8. FileOutputStream
   : output stream that writes to a file
9. FilterInputStream
   : implements InputStream
10. FilterOutputStream
    : implements OutputStream
11. InputStream
    : abstract class that describes stream input
12. ObjectInputStream
    : input stream for objects
13. ObjectOutputStream
    : output stream for objects
14. OutputStream
    : abstract class that describes stream output
15. PipedInputStream
    : Input pipe
16. PipedOutputStream
    : Output pipe
17. PrintStream
    : output stream that contains **print()** and **println()**
18. PushbackInputStream
    : input stream that allows bytes to be returned to the input stream
19. SequenceInputStream
    : input stream that is a combination of two or more input streams
    : that will be read sequentially, one after the other

> Pipes in Java IO provides the ability for two threads running in the same JVM to communicate.

#### The Character Stream Classes
> two class hierarchies, at the top are two abstract classes: **Reader and Writer**
> **handle Unicode character streams**

- BufferedReader
  : buffered input character stream
- BufferedWriter
  : buffered output character stream
- CharArrayReader
  : input stream that reads from a character array
- CharArrayWriter
  : output stream that writes to a character array
- FileReader
  : input stream that reads from a file
- FileWriter
  : output stream that writes to a file
- FilterReader
  : filtered reader
- FilterWriter
  : filtered writer
- InputStreamReader
  : input stream that translates bytes to characters
- OutputStreamWriter
  : output stream that translates characters to bytes
- LineNumberReader
  : input stream that counts lines
- PipedReader
  : input pipe
- PipedWriter
  : output pipe
- PrintWriter
  : output stream that contains **print()** and **println()**
- PushbackReader
  : input stream that allows characters to be returned to the input stream
- Reader
  : abstract class that describes character stream input
- Writer
  : abstract class that describes character stream output
- StringReader
  : input stream that reads from a string
- StringWriter
  : input stream that writes to a string

### The Predefined Streams
> all Java programs automatically import the **java.lang**
> this package defines a class called **System**, which encapsulates several aspects of the run-time environment
> **Stream** also contains three predefined stream: **in, out ane err**, which are **public static final**

- **System.out**
: refers to the standard output stream, by default is the console

- **System.in**
: refers to standard input, which is the keyboard by default

- **System.err**
: refers to the standard error stream, which also is the console by default

- **System.in** are InputStream
- **System.out and System.err** are PrintStream
