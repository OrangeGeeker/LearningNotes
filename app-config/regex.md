# 正则

## 语法

### 元字符

| 语法 | 含义                                          |
| ---- | --------------------------------------------- |
| \w   | 匹配字母、数字、下划线，等价于 [a-zA-Z0-9]    |
| \W   | 匹配非字母、数字、下划线，等价于 [^a-za-z0-9] |
| \d   | 匹配数字，等价于 [0-9]                        |
| \D   | 匹配非数字，等价于 [^0-9]                     |
| \s   | 匹配任何空白字符，包括空格、制表符、换页符    |
| \S   | 匹配任何非空白字符，等价于 [^ \f\n\r\t\v]     |
| .    | 匹配除换行符（\r、\n）之外的任何字符          |
| \f   | 匹配换页符，等价于 \x0c 和 \cL                |
| \n   | 匹配换行符，等价于 \x0a 和 \cJ                |
| \r   | 匹配回车符，等价于 \x0d 和 \cM                |
| \t   | 匹配制表符，等价于 \x09 和 \cl                |
| \v   | 匹配垂直制表符，等价于 \x0b 和 \cK            |
| \p   | 匹配 CR/LR 等价于 \r\n                        |
| ^    | 匹配输入字符串开始的位置                      |
| \$   | 匹配输入字符串结束的位置                      |
| \b   | 匹配一个单词边界，也就是单词空格的位置        |
| \B   | 与 \b 相反                                    |

`ing\b` 匹配 ing 结尾的单词

`1\d\d\d\d\d\d\d\d\d` 匹配 11 位手机号

### 区间

匹配中括号内的任意字符（与顺序无关），括号内的符号无需转义直接进行表示，例如

`[-\w.+]` 表示匹配符号 -，字母、数字、下划线，符号.，符号+

`[0-9a-zA-Z]` 匹配数字、字母

`[^ ]` 匹配除了方括号内的任意字符

### 限定符

| 字符  | 含义                                         |
| ----- | -------------------------------------------- |
| \*    | 匹配前面的子表达式 0 次或多次，等价于 {0,}   |
| +     | 匹配前面的子表达式 1 次或多次，等价于 {1,}   |
| ?     | 匹配前面的子表达式 0 次或 1 次，等价于 {0,1} |
| {n}   | 匹配确定 n 次                                |
| {n,}  | 匹配至少 n 次                                |
| {n,m} | 匹配 n 次至 m 次                             |

`[0-9]{9}@qq.com` 匹配 9 位数 qq 邮箱

`\d{17}[0-9Xx]|\d{15}` 匹配身份证号

### 转义字符

`{ } [ ] ( ) / \ + * . $ | ?` 这些特殊字符需要进行转义，转义符号是 `\`

### 子表达式

括号组成 `(\d)([a-z]*)`

### 运算符

`|` 或运算