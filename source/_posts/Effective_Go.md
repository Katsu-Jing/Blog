# Effective Go

## 概要

Go 是一门新兴语言。虽然它借鉴了现有语言的理念，但是 Go 语言特有的特性使得高效的 Go 程序明显区别于其他语言写成的程序。直接将一段 C++或 Java 程序翻译为 Go 是不会有什么令人满意的结果的。Java 程序就应该用 Java 语言写，而不是 Go。从 Go 的角度思考问题，可以写出更成功和有别于其他的程序。所以，想要写好 Go 程序，理解他的特点和语言风格是非常重要的。同时，遵守已经约定俗成的 Go 编程规范也很重要，比如命名规则、格式化风格、程序结构等等，只有这样才能让其他 Go 程序员更容易的理解你的程序。

这个文档给出了编写干净地道的 Go 程序的建议。

## 格式化

格式问题一直备受讨论，但是却最不重要。人们可以适应不同的格式化风格，但最好别那样。如果每个人都遵守相同的风格，那么花在格式化上的时间就少的多。问题在于如何优雅的实现这个目标，而不需要制定冗长的格式化规范文档。

Go 不同于其他语言，它让机器来做大部分的格式化工作。`gofmt`命令读入一段 Go 程序，输出格式化好的源代码，拥有标准的缩进和对齐，保留并格式化注释。如果你想知道怎么处理新的版面，运行`gofmt`；如果排版不对就手动排一下（或者给 gofmt 提 bug），别在这上面浪费时间。

举例说明：你不需要对齐注释和变量，gofmt 会帮你做好。

```go
type T struct {
    name string // name of the object
    value int // its value
}
```

gofmt 会自动对齐变量和注释：

```golang
type T struct {
    name    string // name of object
    value   int    // its value
}
```

标准包中的 Go 代码都已经用 gofmt 格式化过。

仍然有一些格式化的细节，非常简短提一下：

> **缩进**
>
> 我们使用 tab 键缩进，gofmt 会默认处理它。只在你一定要用空格的地方使用空格。
>
> **单行长度**
>
> Go 没有行长限制，不用担心超长。如果感觉一行代码太长了，可以包裹它然后用一个 tab 来缩进
>
> **圆括号**
>
> Go 使用圆括号的地方比 C 和 Java 更少：控制结构（if, for, switch）在他们的语法定义中都没有圆括号。同时，操作符的优先级更短更清晰，例如：
> `x<<8 + y<<16`优先级正如空格所暗示的那样，这和其他语言是不同的。

## 注释

Go 提供 C 风格的 `/* */` 块注释和 C++风格的 `//` 行注释。行注释更常用；块注释几乎只作为包注释在用，但是在表达式内部使用以及注释大量代码时也比较常用。

godoc 程序会处理 Go 的源文件，导出包内容的文档。顶层声明前的注释（两者间不能有空行）会被导出成该声明的解释性文档。这些注释的样式和内容决定了 godoc 导出的文档的质量。

每个包都应该有包注释。包注释是写在 package 语句前的块注释。多文件的包，包注释只需要出现在一个文件中就可以。包注释应该介绍一下这个包，从总体上给出和这个包相关的信息。它会出现在 godoc 页的最前面。

```go
/*
Package regexp implements a simple library for regular expressions.

The syntax of the regular expressions accepted is:

    regexp:
        concatenation { '|' concatenation }
    concatenation:
        { closure }
    closure:
        term [ '*' | '+' | '?' ]
    term:
        '^'
        '$'
        '.'
        character
        '[' [ '^' ] character-ranges ']'
        '(' regexp ')'
*/
package regexp
```

如果这个包比较简单，那么包注释也可以比较简短：

```golang
// Package path implements utility routines for
// manipulating slash-separated filename paths.
```

注释不需要额外的格式化，例如在左边放一排星号这种。生成的输出甚至有可能不是定宽字体，所以不要使用空格来排版，godoc 类似 gofmt，会帮你处理这些。注释会被视为纯文本，不会被解析，所以像 HTML 或其他标签都会原封不动的输出，例如`_this_`这种，输出依然是`_this_`。godoc 会做的一个调整是，它会把缩进的文本用定宽字体来显示，这样更适合显示代码片段。

godoc 的格式化完全由注释内容决定，甚至有的注释 godoc 根本不会 reformat。所以，在写注释的时候请确保它的整洁：使用正确的拼写、标点和句子结构，长的行要分行等等。

在包内，紧邻顶级声明前的注释会自动作为这个声明的文档注释。程序中每个导出的变量都应该有文档注释。

文档注释最好是完整的句子，可以适应多样的自动化展示。注释的第一句话应该以被定义的名字开头，作为一句话的总结，下面是个例子：

```golang
// Compile parses a regular expression and returns, if successful,
// a Regexp that can be used to match against text.
func Compile(str string) (*Regexp, error) {
```

如果每个文档注释都是以它描述的对象开头，godoc 产生的输出就会更适合用 grep 搜索。设想一下你忘记了函数"Compile"的名字，但是想找解析正则表达式的函数，你就可以用以下命令来找：

```bash
godoc regexp | grep -i parse
```

如果这个包里的所有函数的开头都是"This function"，grep 命令就没什么帮助了。但正是因为这个包的文档注释都是以变量名开始，你就能看到以下结果，这会帮你回忆起你想找的那个词。

```bash
$ godoc regexp | grep parse
    Compile parses a regular expression and returns, if successful, a Regexp
    parsed. It simplifies safe initialization of global variables holding
    cannot be parsed. It simplifies safe initialization of global variables
$
```

Go 语言的声明语法允许对声明分组。一行文档注释可以给一组相关的常数或变量注释，但是因为要给一组声明注释，所以这种注释都比较笼统。

```golang
// Error codes returned by failures to parse an expression.
var (
    ErrInternal      = errors.New("regexp: internal error")
    ErrUnmatchedLpar = errors.New("regexp: unmatched '('")
    ErrUnmatchedRpar = errors.New("regexp: unmatched ')'")
    ...
)
```

分组也可以暗示组员之间的关系，比如一组受信号量保护的变量：

```golang
var (
    countLock   sync.Mutex
    inputCount  uint32
    outputCount uint32
    errorCount  uint32
)
```

## 命名规范

命名规范对于 Go 和其他编程语言一样，都是非常重要的。Go 语言中的命名甚至有语义上的作用：命名在包外的可见性取决于它的首字母是否大写。所以花一点时间解释一下 Go 语言的命名规范还是有必要的。

### 包的命名

当一个包被 import 后，包的名字就会变成包内容的 accessor。在`import "bytes"`后，你可以这样访问它的内容：`bytes.Buffer`。包的使用者都使用同样的名字来访问包的内容，这就要求包的命名要优雅：短小、简洁、容易记忆。按照惯例，包的命名是全小写的单个单词；不能用下划线或者驼峰。注意让包名简短，因为每个使用者都要去敲这个名字。不要担心名字冲突。包名只是 import 使用的默认名称；它不需要在整个源代码中全局唯一，在极少的命名冲突的情况下，import 的包可以用其他名字。发生命名冲突的情况是罕见的，因为在 import 中的文件名会决定到底哪个包被导入了。

另一个惯例是，包的名字是它所在文件路径的 base name。例如在`/src/encoding/base64`中的包，导入时这么写：`encoding/base64`，包的名字却是 base64，不是 encoding_base64 也不是 encodingBase64.

包的导入方使用包名来引用包的内容，所以，在导出包中的变量时可以利用这一点来避免啰嗦（不要使用`import .`表示法，这种表示法可以简化那些只能在包外运行的测试，除此之外都不应使用）。举例来说，在`bufio`中定义的带缓冲的 reader 被命名为`Reader`而不是`BufReader`，因为在使用者看来他是`bufio.Reader`，这已经是很简单明了的名字了。同样的，因为被引入的内容永远要用他们的包名来引用，`bufio.Reader`就不会和`io.Reader`冲突。类似的，构造`ring.Ring`的函数（构造函数）或许应该命名为`NewRing`，但是因为`Ring`是这个包导出的唯一一个类型，而包的名字又是`ring`，构造函数就可以简单的命名为`New`.在使用者看来，他就是`ring.New`。利用好这种包结构，可以帮你更好的命名。

另一个简短的例子是`once.Do`。`once.Do(setup)`可读性很好，可是如果写成`once.DoOrWaitUntilDone(setup)`并不会更好。更长的名字并不一定会让可读性更好。良好的注释常常比一个冗长的名字更有帮助。

## Getters

Go 不提供对 getter 和 setter 的自动支持。由你来提供 getter 和 setter 方法并没有什么错，而且有时还会更好。但是 getter 方法的开头并没有必要是 Get，这也不符合 Go 语言的惯用法。如果你有一个字段是 owner（小写，私有），那么对应的 getter 方法是 Owner（大写，公有）而不是 GetOwner。大写开头的命名提供了区别于对应字段的钩子。setter 方法就会是 SetOwner。这两个命名可读性都很好。

```golang
owner := obj.Owner()
if owner != user {
    obj.SetOwner(user)
}
```

## 接口命名

按照惯例，单方法接口被命名为方法名加 er 结尾，或者类似的构词法构成一个名词：`Reader, Writer, Formatter, CloseNotifier`等。

有一些命名，把他们和他们所拥有的方法名保护起来是更有效的。例如`Read, Write, Close, Flush, String`等都有比较权威的含义和表示。为了避免歧义，不要把你的方法命名成这些名字，除非他们有一样的含义。
