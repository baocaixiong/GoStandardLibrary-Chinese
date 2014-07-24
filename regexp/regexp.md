# regexp 包

import "regexp"

---
[regexp](http://godoc.golangtc.com/pkg/regexp/)

## 简介

regexp 包实现了正则表达式搜索。

本包的语法接受类似Perl, Python和其他类似的语言。更加准确的说，本包语法是除了C意外对RE2和 http://code.google.com/p/re2/wiki/Syntax 页面的准确描述。语法的简述，请运行:
    - godoc regexp/syntax

本包提供的正则表达式实现会保证运行时现行输入的大小。（此属性在大多数开源的正则表示式实现上都不能保证）。关于此属性的信息请查看：
    - http://swtch.com/~rsc/regexp/regexp1.html
或者任何其他有关自动机理(?)的书籍。

所有的表达式语句都是UTF-8编码。
