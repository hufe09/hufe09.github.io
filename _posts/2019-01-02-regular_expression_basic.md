---
layout: post
title: "Regular Expression Basic"
subtitle: '正则表达式基础'
author: "Hufe"
header-img: "img/post-bg-python.jpg"
header-mask: 0.3
tags:
  - Regular Expression
---

# 正则表达式基础


<figure>
    <table>
        <tbody>
        <tr>
            <th width="15%"></th>
            <th width="15%">语法</th>
            <th width="40%">说明</th>
            <th width="20%">表达式实例</th>
            <th width="20%">匹配字符串实例</th>
        </tr>
        <tr>
            <td rowspan="3" align="middle" valign="middle">一般字符</td>
            <td bgcolor="#eeeeee"> .</td>
            <td> 点号：匹配任意字符，换行符(\n)除外</td>
            <td align="middle" valign="middle"> a.c</td>
            <td> abc<br> adc</td>
        </tr>
        <tr>
            <td bgcolor="#eeeeee"> \</td>
            <td> 转义符：使其后的一个字符改变原来的意思，比如匹配*，可以使用\*</td>
            <td align="middle" valign="middle"> a\.c <br> a\*c <br> a\\c</td>
            <td> a.c <br> a*c <br> a\c</td>
        </tr>
        <tr>
            <td bgcolor="#eeeeee"> [...] <br><br> 中间的三个点表示一个或多个字符</td>
            <td>
                字符集合：对应的位置可以是字符集中的任意一个字符。字符集中字符可以逐个列出，也可以给出范围，比如所有小写字符可以写成[a-z]，如果字符集中的第一个字符是上尖括号(^)。那么表示取反，[^a-z]表示所有非小写字母的字符。<br>
                [0123456789]可以写成[0-9]。需要注意的是，所有特殊字符在字符集中都失去原有特殊含义，如果要用到特殊字符，可以在特殊字符前面加上转义符，进行转义。
            </td>
            <td align="middle" valign="middle"> a[sd]f</td>
            <td> asf <br> Adf</td>
        </tr>
        <tr>
            <td rowspan="6" align="middle" valign="middle">数量词</td>
            <td bgcolor="#eeeeee"> *</td>
            <td> 0个或多个字符（贪婪匹配），尽可能多取</td>
            <td align="middle" valign="middle"> 2[a-z]*</td>
            <td> 2 <br> 2a <br> 2asw <br> 2wedqw</td>
        </tr>
        <tr>
            <td bgcolor="#eeeeee"> +</td>
            <td> 1个或多个字符（贪婪匹配），尽可能多取</td>
            <td align="middle" valign="middle"> 2[a-z]+</td>
            <td> 2a <br> 2asw <br> 2wedqw</td>
        </tr>
        <tr>
            <td bgcolor="#eeeeee"> ?</td>
            <td> 0个或1个字符（贪婪匹配），尽可能多取</td>
            <td align="middle" valign="middle"> 2[a-z]?</td>
            <td> 2 <br> 2a</td>
        </tr>
        <tr>
            <td bgcolor="#eeeeee"> {m}</td>
            <td> 对于前一个字符重复m次</td>
            <td align="middle" valign="middle"> as{2}d</td>
            <td> assd</td>
        </tr>
        <tr>
            <td bgcolor="#eeeeee"> {m,n}</td>
            <td> 对于前一个字符重复m到n次，m和n可以省略，若省略m则表示匹配0到n次；若省略n则表示匹配m到无限次</td>
            <td align="middle" valign="middle"> as{1,2}d</td>
            <td> asd <br> assd</td>
        </tr>
        <tr>
            <td bgcolor="#eeeeee"> *? <br> +? <br> ?? <br> {m,n}?</td>
            <td> 使*、+、??、{m,n}变成非贪婪模式，尽可能少取</td>
            <td align="middle" valign="middle"></td>
            <td></td>
        </tr>
        <tr>
            <td rowspan="2" align="middle" valign="middle">边界匹配</td>
            <td bgcolor="#eeeeee"> ^</td>
            <td> 匹配字符串的开头，如果以行为单位则匹配行首</td>
            <td align="middle" valign="middle"> ^asd</td>
            <td> asd</td>
        </tr>
        <tr>
            <td bgcolor="#eeeeee"> $</td>
            <td> 匹配字符串的结尾，如果以行为单位则匹配行尾</td>
            <td align="middle" valign="middle"> asd$</td>
            <td> asd</td>
        </tr>
        <tr>
            <td rowspan="5" align="middle" valign="middle">逻辑分组</td>
            <td bgcolor="#eeeeee"> |</td>
            <td> |表示或的意思，在其左右两侧的表达式中任意匹配一个，在匹配时，总是先匹配左侧的表达式，一旦匹配成功则跳过匹配右侧表达式；<br>如果|没有在圆括号内，则它的范围是整个表达式</td>
            <td align="middle" valign="middle"> asd|qwe</td>
            <td> asd <br> qwe</td>
        </tr>
        <tr>
            <td bgcolor="#eeeeee"> (...)</td>
            <td> 圆括号中的表达式被称为分组，从左向右，以分组的左括号为标志，第一个出现的分组的组号为1，第二个为2，以此类推。</td>
            <td align="middle" valign="middle"> (asd){2}</td>
            <td> asdasd</td>
        </tr>
        <tr>
            <td bgcolor="#eeeeee"> \number</td>
            <td> 引用编号为number的分组，注意：这里的引用是后向引用，引用的仅仅是匹配到的文本内容，而不是正则表达式！</td>
            <td align="middle" valign="middle"> ([0-9])([a-z]\1)</td>
            <td> 1a1</td>
        </tr>
        <tr>
            <td bgcolor="#eeeeee"> (?P&lt;name&gt;...)</td>
            <td> 在分组的同时，给当前组起一个别名，别名的命名规则和变量一样</td>
            <td align="middle" valign="middle"> (?P&lt;id&gt;asd){2}</td>
            <td> asdasd</td>
        </tr>
        <tr>
            <td bgcolor="#eeeeee"> (?P=name)</td>
            <td> 引用别名为name的分组匹配到的内容</td>
            <td align="middle" valign="middle"> (?P&lt;id&gt;\d)asd(?P=id)</td>
            <td> 1asd1 <br> 3asd3</td>
        </tr>
        <tr>
            <td rowspan="10" align="middle" valign="middle">预定义</td>
            <td bgcolor="#eeeeee"> \d</td>
            <td> 数字[0-9]</td>
            <td align="middle" valign="middle"> \d+</td>
            <td> 15 <br> 456 <br> 789</td>
        </tr>
        <tr>
            <td bgcolor="#eeeeee"> \D</td>
            <td> 非数字 [^0-9]</td>
            <td align="middle" valign="middle"></td>
            <td></td>
        </tr>
        <tr>
            <td bgcolor="#eeeeee"> \s</td>
            <td> 空白字符：[\nt\n\r\f\v]包含空格</td>
            <td align="middle" valign="middle"></td>
            <td></td>
        </tr>
        <tr>
            <td bgcolor="#eeeeee"> \S</td>
            <td> 非空白字符：[^\nt\n\r\f\v]包含空格</td>
            <td align="middle" valign="middle"></td>
            <td></td>
        </tr>
        <tr>
            <td bgcolor="#eeeeee"> \w</td>
            <td> 单词字符：[a-zA-Z0-9_]</td>
            <td align="middle" valign="middle"></td>
            <td></td>
        </tr>
        <tr>
            <td bgcolor="#eeeeee"> \W</td>
            <td> 非单词字符：[^a-zA-Z0-9_]</td>
            <td align="middle" valign="middle"></td>
            <td></td>
        </tr>
        <tr>
            <td bgcolor="#eeeeee"> \A</td>
            <td> 匹配字符串开头</td>
            <td align="middle" valign="middle"></td>
            <td></td>
        </tr>
        <tr>
            <td bgcolor="#eeeeee"> \Z</td>
            <td> 匹配字符串结尾</td>
            <td align="middle" valign="middle"></td>
            <td></td>
        </tr>
        <tr>
            <td bgcolor="#eeeeee"> \b</td>
            <td> 匹配位于开始或结尾的空字符串</td>
            <td align="middle" valign="middle"> r'\bfoo\b'</td>
            <td> Matches 'foo','foo.','(foo)','bar foo baz' <br> But not 'foobar' or 'foo3'</td>
        </tr>
        <tr>
            <td bgcolor="#eeeeee"> \B</td>
            <td> 匹配不位于开始或结尾的空字符串</td>
            <td align="middle" valign="middle"> r'py\B'</td>
            <td> Matches 'python','py3','py2', <br> But not 'py.' or 'py!'</td>
        </tr>
        </tbody>
    </table>
</figure>

## 常用正则表达式
<figure>
    <table>
        <thead>
        <tr>
            <th style='text-align:center;'>用户名</th>
            <th style='text-align:left;'>/^[a-z0-9_-]{3,16}$/</th>
        </tr>
        </thead>
        <tbody>
        <tr>
            <td style='text-align:center;'>密码</td>
            <td style='text-align:left;'>/^[a-z0-9_-]{6,18}$/</td>
        </tr>
        <tr>
            <td style='text-align:center;'>十六进制值</td>
            <td style='text-align:left;'>/^#?([a-f0-9]{6}|[a-f0-9]{3})$/</td>
        </tr>
        <tr>
            <td style='text-align:center;'>电子邮箱</td>
            <td style='text-align:left;'>/^([a-z0-9_.-]+)@([\da-z.-]+).([a-z.]{2,6})/
                /^[a-z\d]+(\.[a-z\d]+)*@([\da-z](-[\da-z])?)+(\.{1,2}[a-z]+)+/
            </td>
        </tr>
        <tr>
            <td style='text-align:center;'>URL</td>
            <td style='text-align:left;'>/^(https?:\/\/)?([\da-z.-]+).([a-z.]{2,6})([\/\w .-]<em>)</em>\/?$/</td>
        </tr>
        <tr>
            <td style='text-align:center;'>IP 地址</td>
            <td style='text-align:left;'>/((2[0-4]\d|25[0-5]|[01]?\d\d?).){3}(2[0-4]\d|25[0-5]|[01]?\d\d?)/
                /^(?:(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?).){3}(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/
            </td>
        </tr>
        <tr>
            <td style='text-align:center;'>HTML 标签</td>
            <td style='text-align:left;'>/^&lt;([a-z]+)([^&lt;]+)<em>(?:&gt;(.</em>)&lt;\/\1&gt;|\s+\/&gt;)$/</td>
        </tr>
        <tr>
            <td style='text-align:center;'>删除代码\注释</td>
            <td style='text-align:left;'>(?&lt;!http:|\S)//.*$</td>
        </tr>
        <tr>
            <td style='text-align:center;'>Unicode编码中的汉字范围</td>
            <td style='text-align:left;'>/^[\u2E80-\u9FFF]+$/</td>
        </tr>
        </tbody>
    </table>
</figure>