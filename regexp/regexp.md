# regexp 包

import "regexp"

---
[regexp](http://godoc.golangtc.com/pkg/regexp/)

## 简介

regexp 包实现了正则表达式搜索。

本包的语法接受类似Perl, Python和其他类似的语言。更加准确的说，本包语法是除了C以外对RE2和 `http://code.google.com/p/re2/wiki/Syntax` 页面的准确描述。语法的简述，请运行:
    
    godoc regexp/syntax

本包提供的正则表达式实现会保证运行时现行输入的大小。（此属性在大多数开源的正则表示式实现上都不能保证）。关于此属性的信息请查看

    http://swtch.com/~rsc/regexp/regexp1.html

或者任何其他有关自动机理(?)的书籍。

所有的表达式语句都是UTF-8编码。

本包中`Regexp`接收16个正则表达式方法去正确的匹配一个需要匹配的字符串。它们的名字可以由这个正则表达式进行匹配:

    Find(All)?(String)?(Submatch)?(Index)?
    
如果`All`是方法名中存在，那么这个表达式将不重叠的连续匹配每一个结果，但是临近上一个空匹配将会忽略。通常没有`All`程序的返回值是包含成功匹配的结果的slice。这些程序会需要一个额外的`int`类型的参数`n`, 如果 `n > 0` 那么这个方法返回多个匹配的字串或者子字串。

如果存在`String`,那么参数也是一个`string`，否则返回一个`[]byte`；返回值会根据参数类型做合适的调整。

如果`Submatch`存在，返回值是一个连续匹配的字串的slice。`Submatch`在正则表达式中匹配带括号的子表达式(又叫做捕获组)，分组的编号从左到右。其中`0`编号是整个表达式的匹配，`1`编号是左起第一个字表达式的匹配，依次类推。

如果`Index`存在，匹配和子匹配会按字节数对被匹配的字符串进行匹配：确认结果`[n*2:n*2+1]`是输入的第`n`个子匹配。如果`n=0`表示整个表达式的匹配。如果`Index`不存在，匹配所有表达式/字表达式，如果输入的`index`是负数，则表示对输入的string不做任何匹配。

还有一些子集的方法可以应用于从`RuneReader`中匹配:

    > MatchReader、FindReaderIndex FindReaderSubmatchIndex

这些子集方法在未来可能还会添加。请注意正则表达式匹配时可能需要检查从Reader中返回的文本之外的匹配，所以这些方法可以`RuneReader`读取到任意输入然后返回结果。
    
(还有一个其他的方法和上面叙述的不同。)

Code:
```go
// 通常在初始化的时候就会编译好表达式对象
// 使用"``"原始字串，避免反斜杠转义 
var validID = regexp.MustCompile(`^[a-z]+\[[0-9]+\]$`)

fmt.Println(validID.MatchString("adam[23]"))
fmt.Println(validID.MatchString("eve[7]"))
fmt.Println(validID.MatchString("Job[48]"))
fmt.Println(validID.MatchString("snakey"))
```
Output:
```go
true
true
false
false
```

## 内容

### func Match(pattern string, b []byte) (matched bool, err error)
检查输入的是否能够匹配一个字节切片。更加复杂的查询需要使用编译(Compile)和完整的`Regexp`接口。

### func MatchReader(pattent string, r io.RundReader) (matched book, err error)

`MatchReader`检查输入的表达式是否能够正确的匹配一个输入的`RunReader`。更加复杂的查询需要使用编译(Compile)和完整的`Regexp`接口。

### func MatchString(pattern string, s string) (matched bool, err error)

`MatchString`检查输入和表达式是否能够正确的匹配输入的字符串。更加复杂的查询需要使用编译(Compile)和完整的`Regexp`接口。

### func QuoteMeta(s string) string

`QuoteMeta`对输入字符串中所有的正则表达式元字符都加上过滤，返回一个可以进行正则匹配的文本。例如, `QuoteMeta("[foo]")`将返回`\[foo\]`

### type Regexp

```
type Regexp struct {
    // 包含过滤后或输出字段
}
```
`Regexp`是一个编译后的正则表达式的表达。一个`Regexp`是一个线程安全的。

### func Compile(expr string) (*Regexp, error)

编译正则表达式并返回。如果处理成功，则返回一个可以用来匹配字符串的`Regexp`对象。
