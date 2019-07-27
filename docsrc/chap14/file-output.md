# File Output（文件输出）

To write data to a file, you need an output stream, which you obtain
by calling **OPEN** with a `:direction` keyword argument of `:output`. When
opening a file for output, **OPEN** assumes the file shouldn't already
exist and will signal an error if it does. However, you can change
that behavior with the `:if-exists` keyword argument. Passing the value
`:supersede` tells **OPEN** to replace the existing file. Passing `:append`
causes **OPEN** to open the existing file such that new data will be
written at the end of the file, while `:overwrite` returns a stream that
will overwrite existing data starting from the beginning of the
file. And passing **NIL** will cause **OPEN** to return **NIL** instead of a
stream if the file already exists. A typical use of **OPEN** for output
looks like this:

为了向一个文件中写数据，你需要一个输出流。你可以通过在调用 **OPEN**
时使用一个值为 `:output` 的 `:direction`
关键字参数来获取它。当你打开一个用于输出的文件时，**OPEN**
会假设该文件不该存在并会在文件存在时报错。但你可以使用 `:if-exists`
关键字参数来改变该行为。传递值 `:supersede` 可以告诉 **OPEN**
来替换已有文件。传递 `:append` 将导致 **OPEN**
打开已有的文件并保证新数据被写到文件结尾处，而 `:overwrite`
返回一个从文件开始处开始的流从而覆盖已有数据。而传递 **NIL**
将导致 **OPEN** 在文件已存在时返回 **NIL**
而不是流。一个典型的使用 **OPEN** 来输出的例子如下所示：

```lisp
(open "/some/file/name.txt" :direction :output :if-exists :supersede)
```

Common Lisp also provides several functions for writing data:
**WRITE-CHAR** writes a single character to the stream. **WRITE-LINE** writes
a string followed by a newline, which will be output as the
appropriate end-of-line character or characters for the
platform. Another function, **WRITE-STRING**, writes a string without
adding any end-of-line characters. Two different functions can print
just a newline: **TERPRI**--short for "terminate print"--unconditionally
prints a newline character, and **FRESH-LINE** prints a newline character
unless the stream is at the beginning of a line. **FRESH-LINE** is handy
when you want to avoid spurious blank lines in textual output
generated by different functions called in sequence. For example,
suppose you have one function that generates output that should always
be followed by a line break and another that should start on a new
line. But assume that if the functions are called one after the other,
you don't want a blank line between the two bits of output. If you use
**FRESH-LINE** at the beginning of the second function, its output will
always start on a new line, but if it's called right after the first,
it won't emit an extra line break.

Common Lisp 也提供了几个用于写数据的函数：**WRITE-CHAR**
会向流中写入一个单一字符；**WRITE-LINE**
写一个字符串并紧跟一个换行，其将被输出成用于当前平台的适当行结束字符或字符序列。另一个函数
**WRITE-STRING** 写一个字符串而不会添加任何行结束符。有两个不同的函数
可以只打印一个换行：**TERPRI**
是 “终止打印（terminate print）”
的简称，即无条件地打印一个换行字符；**FRESH-LINE**
打印一个换行字符，除非该流已经在一行的开始处。在想要避免由按顺序调用的不同函数所生成的文本输出中的额外换行时，**FRESH-LINE**
很有用。例如，假设一个函数在其生成输出时总是带有一个换行，而另一个函数应当每次从一个新行开始输出。但假设这两个函数被依次调用，而你又不希望在两个输出操作之间产生一个空行，那么如果你在第二个函数开始处使用
**FRESH-LINE**，那么它的输出将总是从一个新行开始。但如果它刚好在前一个函数之后调用，则它将不会产生一个额外换行。

Several functions output Lisp data as s-expressions: **PRINT** prints an
s-expression preceded by an end-of-line and followed by a space. **PRIN1**
prints just the s-expression. And the function **PPRINT** prints
s-expressions like **PRINT** and **PRIN1** but using the "pretty printer,"
which tries to print its output in an aesthetically pleasing way.

有几个函数会将 Lisp 数据输出成 S-表达式：**PRINT**
打印一个 S-表达式，前缀一个换行及一个空格；**PRIN1** 只打印 S-表达式；而函数
**PPRINT** 会像 **PRINT** 和 **PRIN1** 那样打印
S-表达式，但使用的是美化打印器（pretty printer），它试图将输出打印得赏心悦目。

However, not all objects can be printed in a form that **READ** will
understand. The variable `*PRINT-READABLY*` controls what happens if you
try to print such an object with **PRINT**, **PRIN1**, or **PPRINT**. When it's
**NIL**, these functions will print the object in a special syntax that's
guaranteed to cause **READ** to signal an error if it tries to read it;
otherwise they will signal an error rather than print the object.

但并非所有对象都能以一种 **READ** 可理解的形式打印出来。当试图使用
**PRINT**、**PRIN1** 或 **PPRINT** 来打印这样一种对象时，变量
`*PRINT-READABLY*` 将会予以控制。当它是 **NIL**
时，这些函数将以一种导致 **READ**
在试图读取时肯定会报错的特殊语法来打印该对象；否则它们将直接报错而不打印该对象。

Another function, **PRINC**, also prints Lisp objects, but in a way
designed for human consumption. For instance, **PRINC** prints strings
without quotation marks. You can generate more elaborate text output
with the incredibly flexible if somewhat arcane **FORMAT** function. I'll
discuss some of the more important details of **FORMAT**, which
essentially defines a mini-language for emitting formatted output, in
Chapter 18.

另一个函数 **PRINC** 也会打印 Lisp
对象，但其工作方式却很适合人们使用。例如，**PRINC**
在打印字符串时不带有引号。你可以使用极其灵活但有时略显神秘的
**FORMAT** 函数来生成更加复杂的文本输出。我将在第 18
章里讨论一些关于 **FORMAT**
的更重要的细节，它从本质上定义了一种用于产生格式化输出的微型语言。

To write binary data to a file, you have to **OPEN** the file with the
same `:element-type` argument as you did to read it: `'(unsigned-byte 8)`.
You can then write individual bytes to the stream with WRITE-BYTE.

为了向一个文件中写入二进制数据，你需要在使用 **OPEN**
打开文件时带有与读取该文件时相同的 `:element-type` 实参，其值为
`'(unsigned-byte 8)`，然后就可以使用 **WRITE-BYTE** 向流中写入单独的字节。

The bulk output function **WRITE-SEQUENCE** accepts both binary and
character streams as long as all the elements of the sequence are of
an appropriate type for the stream, either characters or bytes. As
with **READ-SEQUENCE**, this function is likely to be quite a bit more
efficient than writing the elements of the sequence one at a time.

批量输出函数 **WRITE-SEQUENCE**
可同时接受二进制和字符流，只要序列中的所有元素都是用于该流的适当类型即可，无论其是字符还是字节。和
**READ-SEQUENCE** 一样，该函数会比每次输出一个序列元素更为高效。