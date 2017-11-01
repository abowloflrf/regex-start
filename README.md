# 正则表达式
## 0.开始
最近一段时间对**正则表达式**的需求比较大，于是去图书馆一口气借下了几本正则相关的书打算最近几天系统学习一下，本文记录正则表达式学习笔记
## 1.匹配单个字符
`.`匹配任意字符
## 2.匹配字符集合
`[]`匹配字符集合：`[123]`：1或2或3;`[a-z]`：一个小写字母

`^`非，表示不含有：`[^a-z]`：不含小写字母
## 3.匹配元字符
### 3.2特殊字符
`\`转义字符，用来匹配特殊字符
### 3.2空白字符
`[\b]`Backspace

`\f`换页符

`\n`换行符

`\r`回车符

`\t`制表符Tab

`\v`垂直制表符

`\s`任意空白字符 等价`[\f\n\r\t\v]`

`\S`任意非空白字符
> `\r\n`为windows所用的文本行结束标签
>
> Unix匹配空白行只使用`\n\n`，不需要`\r`
### 3.3匹配数字
`\d`任意数组，等价[0-9]

`\D`任意非数字，等价[^0-9]
### 3.4匹配字母和数字
`\w`任意字母数字或下划线 等价`[a-zA-Z0-9_]`

`\W`任意非字母数字下划线
### 3.5匹配十六进制或八进制
可以通过某个特定字符的十六进制或者八进制数值来匹配

`\x`匹配十六进制字符，如`\x0A`对应于ASCII字符10(换行符)，因此效果等同于`\n`

`\0`匹配八进制字符，`\011`对应ASCII字符9(制表符)，效果等同于`\t`
## 4.重复匹配
`+`在一个给定字符或字符集合后加上`+`表示匹配多次重复

`*`与`+`用法相同，不同点在于其可以匹配0个字符，而`+`至少需要匹配一个

- 电子邮件地址匹配

`\w+[\w.]*@[\w.]+\.\w+`

@前最开头`\w+`必须是字母组合不可为点，`[\w.]*`往后可以是多个或0个点和字母的组合，随后匹配一个@不需要使用反斜杠转义，@之后必须是字母组合加点加字母组合表示域名，由于邮件地址可以有子域名因此可以匹配多个字母加点的组合使用`[\w.]+\.\w+`

### 4.1允许匹配0个
`?`匹配零个或一个字符

- 网址匹配

`https?://[\w./]+`

### 4.2匹配重复次数
`{6}`在字符或字符集后使用以指定匹配重复的次数

- 匹配十六位RGB色值

`#[0-9a-fA-F]{6}`

`{2,6}`设定匹配重复次数区间

`{6,}`至少重复6次
### 4.3防止过度匹配
`?`匹配尽可能少的字符

`*`=>`*?`

`+`=>`+?`

`{n,}`=>`{n,}?`
## 5.位置匹配
### 5.1单词边界
`\b`表示单词边界，开头或结尾

如`\bcat\b`就是匹配一句话中的cat这个单词，而不会匹配某个包含cat三个字母长单词中的cat

> 实际上`\b`匹配的是一个位置，这个位置位于一个能组成单词的字符等同于`\w`即字母数字和下划线与一个不能组成单词的字符`\W`之间的位置

### 5.2字符串边界
`^`匹配字符串开头

`$`匹配字符串结尾

- 匹配xml文档的开头标记

`^\s*<\?xml.*\?>`

- 匹配html文档最后的`</html>`标记

`</[Hh][Tt][Mm][Ll]>\s*$`
### 6.子表达式
正则表达式中的自表达式就是使用小括号对表达式进行分组归类等，如要匹配文本中的多个`&nbps`，使用`&nbps{2,}`会错误的匹配，只会匹配到多个`s`而不是`&nbps`，这里使用小括号将其包括起来：`(&nbps){2,}`就能成功匹配到多组给定字符集

- ip地址

`(\d{1,3}\.){3}\d{1,3}`

- 年份

`(19|20)\d{2}`

- **使用子表达式的嵌套完善ip地址匹配**

上面对ip的匹配依然有很多缺陷，如超过255的三位数字也会匹配进去，我们了解到一个ip地址组成每一段的数组需要满足以下条件

1. 任意以1位或2位数字
2. 任意以1开头的三位数字
3. 任意以2开头、第二位在0-4之间的三位数字
4. 任意以25开头，第三位在0-5之间的三位数字

`(((\d{1,2})|(1\d{2})|(2[0-4]\d)|(25[0-5]))\.){3}((\d{1,2})|(1\d{2})|(2[0-4]\d)|(25[0-5]))`

## 7.回溯引用
回溯引用允许正则表达式应用前面的匹配结果

- 匹配连续两个相同的单词

`[ ]+(\w+)[ ]+\1`

这个表达式的回溯引用是末尾的`\1`，这里的`\1`表示的是第一个子表达式即前面小括号里面的匹配结果

- 匹配html中的h1-h6标签

`<[Hh]([1-6])>.*?</[Hh]\1>`

这里使用回溯引用就不会错误地匹配到`<h2></h3>`中的内容

### 替换
...
## 8.前后查找
前面所述的正则匹配都是匹配指定满足的文本，而这里所使用的查找是匹配位于满足指定要求文本其相对位置的一段指定文本
### 向前查找
`?=`需要匹配的文本在后面，需要查找的文本在前面

- 提取url中的协议名

`.+(?=:)`

### 向后查找
`?<=`

- 提取$后的价格数字

`(?<=\$)[\d.]+`

### 前后查找组合
- 匹配html标签`<html></html>`内的内容

`(?>=<html>).*(?=</html>)`

### 对前后查找取非
- 向前查找与向前查找取非（即不与给定模式匹配的文本
）
`(?=)`,`(?!)`

- 向后查找与向后查找取非

`(?<=)`,`(?<!)`

