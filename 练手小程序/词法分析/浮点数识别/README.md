<!--
 * @Author: baisichen
 * @Date: 2021-05-19 20:41:10
 * @LastEditTime: 2021-05-21 16:40:23
 * @LastEditors: baisichen
 * @Description: 
-->
# 词法分析-浮点数识别
> 参考：http://www.cppblog.com/woaidongmao/archive/2009/09/25/97245.aspx
> 
> 优秀实践：https://github.com/ZYBaisichen/LeetCodeBsc/tree/main/%E5%89%91%E6%8C%87offer/20.%E8%A1%A8%E7%A4%BA%E6%95%B0%E5%80%BC%E7%9A%84%E5%AD%97%E7%AC%A6%E4%B8%B2

# 总体设计

## 输入
屏幕输入或文本或程序中的字符串作为输入
### 输出
识别出的一个浮点数字串
### 测试集
可以使用https://leetcode-cn.com/problems/biao-shi-shu-zhi-de-zi-fu-chuan-lcof

### 一个浮点数的定义：
- 符号部分，+或-
- 整数部分，由数字串组成
- 小数部分，以字符'.'为开始后跟数字串
- 指数部分，由E或e开头后跟带符号的数字串(这里暂不考虑指数为小数的情况)

### 确定有限自动机
因为场景比较简单，这里直接给出确定有限自动机. 这里面并不是严格意义上的确定有限自动机，其中+/-和e/E都是跳转到同一个状态，为了书写方便才这样写的，在状态转移表中需要拆开。
<div align=center>
<img src="https://github.com/ZYBaisichen/MarkdownImages/blob/main/float_digit_finite_automata.jpeg" />
</div>

### 状态表
|状态|输入|下一个状态|
|---|---|---|
|0|开始状态||
|0|+|1|
|0|-|1|
|0|digit|2|
|1|digit|2|
|2|digit|2|
|2|.|3|
|2|e|5|
|2|E|5|
|2|other|8|
|3|digit|4|
|3|other|9|
|4|digit|4|
|4|e|5|
|4|E|5|
|4|other|8|
|5|+|6|
|5|-|6|
|5|digit|7|
|5|other|9|
|6|digit|7|
|6|other|9|
|7|digit|7|
|7|other|8|
|8|结束状态||
|9|错误状态||

### 详细设计
#### FoatPoint
类名，识别浮点数.
- 成员变量
  - _f： 映射关系表
  - _state: 当前状态
  - _curToken: 当前识别的结果
  - _input_str: 输入的字符串
- 成员函数
  - getNextToken
  - isDigit
  - getChar
  - error_log
  - getCurToken

#### _f
状态转移关系表，_f[state][ch]中存储在state状态下输入ch之后的下一个状态号nextstate

#### _state 
记录当前状态，每次getChar时根据状态转移关系表改变状态

#### _curToken
当前已经识别为合法的数字串，当两个结束状态时清空

#### _input_str
要解析的输入字符串，初始化时指定

#### getNextToken()
主体程序，获得下一个浮点数

#### isDigit()
识别是否为数字的函数, 判断当前输入的字符是否在0-9之间

#### getChar()
获取下一个字符

#### error_log()
错误处理函数，处理状态9的报错，并打印日志

#### getCurToken()
拿到当前的识别出的数字字符串

