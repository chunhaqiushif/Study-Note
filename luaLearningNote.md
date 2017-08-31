## Lua学习笔记
----
### 一些初学需要用到的指令：
* 执行文件
  1. 执行某个lua文件，定位到文件夹打开cmd，输入`Lua XXX.lua`就能打开该lua文本文件。
  2. 除了将文件保存到一个文件再执行外，还可以在交互模式中运行解释器：
    * 在命令行中输入`lua -i prog`(prog为程序块)
    * 使用函数`dofile()`，如`dofile("XXX.lua")`，函数内为文件路径
    * 结束交互模式：
      1. end-of-file控制字符：(DOS/WIN)`Ctrl+Z`,(UNIX)`Ctrl+D`
      2. 调用操作系统库exit函数：`os.exit()`
* 清屏：`os.execute("cls")`
---
### Lua指令参数
解释器程序的用法：`lua [选项参数] [脚本[参数] ]`
* `-e`：可以直接在某个命令行中输入代码，如：  `lua -e "print(math.sin(12))"`
* `-l`：用于加载库文件
* `-i`：表示运行完其他命令行参数后进入交互模式，如：`lua -i -l a -e "x=10"` (先加载库文件a，再执行赋值语句x=10，最后显示一个交互模式的命令提示符)
* `lua script x y z`：运行脚本，解释器在运行脚本前，会用所有的命令行参数创建一个名为“arg”的table。脚本名称位于索引0上，它的第一个参数位于索引1，以此类推。而在“script”之前的所有选项参数则位于负数索引上。如：`lua -e "sin=math.sin" script a b`
---
### Lua类型与值的某些特性
#### string
* 单引号与双引号都可以使用。若字符串本身包含引号，可以使用另一种引号来界定字面字符串，或者使用反斜杠对引号进行转义。除此之外应该坚持在程序中使用相同类型的引号。
* 数值可以由转义序列给出：`\<ddd>`，其中`<ddd>`是一个至多3个十进制数字组成的序列。例如：`alo\n123\`与`\97lo \10\04923`是相同的。
* 匹配的双方括号界定一个字母字符串：<pre>```
    page = [[
    <html>
    <head>
    <title>An HTML Page</title>
    </head>
    <body>
    <a href="html://www.lua.org">Lua</a>
    </body>
    </html>
    ]]
  ```</pre>
在两个方括号间加上任意数量的等号：`[===[`&`]===]`，如果双左双右括号内的等号数量不等，Lua会忽略它。
  1. 适用于嵌入任意的字符串内容而不用加以转义；
  2. 适用于注释，如：`--[=[`&`]=]`。可以简化注释了那些“已经包含了注释块”的代码。


 * 字符连接操作符：“`..`”（当直接在一个数字后面输入他的时候必须要用一个空格来分隔），如：`print(10 .. 20)`等同于`print(1020)`
 * 长度操作符：可以获得字符串长度。在字符串前放置操作符“`#`”，如：<pre>
 a = "hello"
 print(#a)
 print(#"good\0bye")</pre>
 第二行将得到5，第三行将得到8


 #### table
 * 可以使用整数也可以使用字符串或是其他类型的值（除了nil）来索引。
 * 语法糖：以字段名命名索引的情况下`a.x = 10`等同于`a["x"] = 10`（需注意是`a["x"]`而不是`a[x]`）
 * 数组索引通常以1作为起始值。
 * 长度操作符在table中的一些习惯写法：
 <pre>
--打印所有的行
for i=1, #a do
  print(a[i])
 end</pre>
<pre>
print(a[#a])    --打印列表a的最后一个值
a[#a] = nill      --删除最后一个值
a[#a+1] = v    --将v添加到列表末尾 </pre>
 <pre>
 --读取一个文件的前十行
 a = {}
 for i=1, 10 do
  a[#a+1] = io.read()
 end</pre>


 * 有关nil：
  未初始化的元素的索引结果都是nil。nil作为界定数组结尾的标志。当一个数组中间含有nil（称为“空隙”）时，长度操作符会认为nil元素就是结尾标记。如果需要处理那些中间含有nil的数组，可以使用函数`table.maxn`，它将返回一个table的最大正索引数。  <pre>a = {}
    a[10000] = 1
    print(#a)          --> 0  </pre>
  <pre>a = {}
  a[10000] = 1
  print(table.maxn(a))    --> 10000  </pre>


 * 转换函数：
  1. tonumber()
  2. tostring()

---
 ### 表达式
 * 关系操作符
   * `~=`表示不等于
 * 逻辑操作符
   * `and`&`or`&`not`
* table构造式
  <pre>--常规构造式
  days = {"Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"}
  --特殊构造式
 a = {x = 10, y = 20}
 --等价构造式
 a = {}; a.x = 10, a.y = 20
 --嵌套构造式表达
polyline = {
       color = "blue", thickness = 2, npoint = 4,
       {x = 0, y = 0},
       {x = -10, y = 0},
       {x = -10, y = 1},
       {x = 0, y = 1},
  }
  --显式构造式表达
opnames = {["+"] = "add", ["-"] = "sub", ["*"] = "mul", ["/"] = "div"} </pre>

 ---
 ### 语句
 #### 多重赋值
 * 诸如：`a, b = 10, 2*x        -->a=10, b = 2*x`
 * 先对等号右边的所有元素求值，再执行赋值
 * 若值的个数少于变量的个数，多余的变量会被赋值为nil
 * 不可进行诸如`a, b, c = 0`的赋值操作（正确方式：`a, b, c = 0, 0, 0`）


 #### 局部变量与块
* do-end显式界定一个块用于避免将变量引入全局
  <pre>do
        local a2 = 2*a
        local d = (b^2 - 4*a*c)^(1/2)
        x1 = (-b + d)/a2
        x2 = (-b - d)/a2
   end      -->a2和d的作用域结束  </pre>

* 初始化赋值的某种习惯写法：
    `local foo = foo`
    创建一个局部变量foo，并将用全局变量foo的值初始化它。如果后续其他函数改变了全局foo的值，
    那么可以在这里先将他的值保存起来。可用于加速再当前作用域中对foo的访问。


#### 控制结构
1. if else then
  <pre>
  if a < 0 than a=0 end</pre>

 <pre>if a < b then return a else return b end</pre>

  <pre>if line >MAXLINES then
      showpage()
      line = 0
   end
</pre>

  <pre>
  if op == "+" then
    r = a + b
  elseif op == "-" then
    r = a - b
  else
    error("invalid operation")
  end
  </pre>
  *Lua不支持switch语句
2. while
  <pre>
   local i =1
   while a[i] do
    print(a[i])
    i = i + 1
   end
  </pre>
3. repeat<pre>
 repeat
  line = io.read()
until line ~= " "
print(line)</pre>
4. for
<pre>
--数字型for
for var=exp1, exp2, exp3 do --执行体位置-- end
for i = 1, f(x) do print(i) end
for i = 10, 1, -1 do print(i) end</pre>
  * var从exp1变化到exp2，每次变化都以exp3作为步长递增var，exp3可选，默认为1

  <pre>
  --泛型for
  --ipairs（用于遍历数组的迭代器函数），i被赋予索引值，v被赋予对应该索引的数组元素值
  for i, v in ipairs(a) do print(v) end
  --pairs（迭代table元素）
  for k in pairs(t) do print(k) end  </pre>

  * 其他迭代器
    * io.line（用于迭代文件中的每行）
    * string.gmatch（用于迭代字符串中单词）
  * 逆向table（表现形式上为索引和值互换）
  <pre>
  revDays = {}
  for k,v in pairs(days) do
    revDays[v] = k
  end  </pre>

5. return & break

---
### 函数
* 函数调用特殊例外情况：一个函数若只有一个参数，并且此参数是一个字面字符串或table构造式，那么圆括号可以省略
<pre>
print "Hello World"   -->   print("Hello World")
dofile 'a.lua'    -->   dofile('a.lua')
print [[a multi-line message]]    -->   print([[a multi-line message]])
f{x=10, y=20}   -->   f({x=10, y=20})
type{}    -->   type({})</pre>

* 冒号操作符：`o.foo(o, x)`的另一种写法是`o:foo(x)`（隐含的将o作为函数的第一个参数）
* Lua可随意调用C语言编写的函数（Lua标准程序库中的函数都是用C编写的）
* 调用函数时提供的实参熟练可以与形参数量不同：
<pre>function f(a,b) return a or b end
f(3)    --> a=3 b=nil
f(3,4)    --> a=3 b=4
f(3,4,5)    --> a=3 b=4 （5被丢弃了）</pre>

#### 多重返回值
<pre>s, e = string.find("hello Lua users", "Lua")
print(s, e)   -->7  9</pre>

<pre>function maxium(a)
  local mi = 1    --最大值索引
  loca m = a[mi]    --最大值
  for i, val in ipairs(a) do
    if val > m then
      mi = i; m =val
    end
  end
  return m, mi    -->只需在return后列出所有的返回值即可
end
print(maxium({8,9,0,1,3,5,7,3}))    --> 9 2</pre>

<pre>function foo0() end
function foo1() return "a" end
function foo2() return "a", "b" end</pre>

<pre>--函数调用的是最后的/仅有的一个表达式，Lua会保留尽可能多的返回值
x, y = foo2()   -- x="a", y="b"
x = foo2()    -- x="a", "b"被丢弃
x, y, z = 10, foo2()    -- x=10, y="a", z="b"</pre>

<pre>--函数没有返回值或者没有足够多的返回值，Lua会用nil补足
x, y = foo0()   -- x="a", y=nil
x, y = foo1()   -- x="a", y=nil
x, y, z = foo2()    -- x="a", y="b", z=nil</pre>

<pre>--函数调用不是一些列表达式的最后一个元素，将只产生一个值
x, y = foo2(), 20   -- x="a", y=20
x, y = foo0(), 20, 30   -- x=nil, y=20, 30被丢弃</pre>

<pre>--函数调用作为另一个函数调用的/仅有的实参时，第一个函数的所有返回值都将
--作为实参传入第二个函数
print(foo0())    -->
print(foo1())    --> a
print(foo2())    --> a  b
print(foo2(), 1)    --> a 1
print(foo2() .. "x")    --> ax（foo2出现在一个表达式中，故返回值为1个）</pre>

<pre>--table构造式可以完整接收一个函数调用的所有结果（当作为最后一个参数时）
t = {foo0()}   -- t = {}
t = {foo1()}   -- t = {"a"}
t = {foo2()}   -- t = {"a", "b"}
t = {foo0(), foo2(), 4}   -- t[1]=nil, t[2]="a", t[3]=4</pre>

<pre>--诸如return f()这样的语句可以返回f()的所有返回值
function foo(i)
  if i == 0 then return foo0()
  elseif i == 1 then return foo1()
  elseif i == 2 then return foo2()
  end
end</pre>

<pre>--将函数放入一对圆括号内，可使函数仅返回一个结果，return语句后面内容不需要括号
print((foo2()))   --> a</pre>

<pre>--unpack，接受一个数组作为参数，并从下标1开始返回该数组的所有元素
print(unpack{10, 20, 30})   --> 10  20  30
a, b = unpack{10, 20, 30}   --> a=10, b=20, 30被丢弃
--调用任意函数f，而所有参数都在数组a中，如：
f = string.find
a = ("hello", "ll")
f(unpack(a))    --> 3  4</pre>



#### 变长参数
* 参数表用`...`代替参数，表示该函数可接受不同数量的实参，如：
<pre>function add(...)
local s = 0
  for i, v in ipairs{...} do
    s = s + v
  end
return s
end
--
print(add(3, 4, 10, 25, 12))    -->54</pre>

<pre>local a, b = ...
--用第一个和第二个变长参数来初始化这两个局部变量</pre>

<pre>--多值恒定式
function id(...) return ... end
--
function foo1(...)
print("calling foo:", ...)
  return foo(...)
end
--foo1函数用于跟踪foo函数的调用，本质与直接调用foo无异</pre>

<pre>--格式化文本(string.format)和输出文本(io.write)函数结合使用
function fwrite(fwt, ...)
  return io.write(string.format(fmt, ...))
end</pre>

  * string.format用法
  >（<a src="http://blog.csdn.net/hello_crayon/article/details/50667927#">详情</a>）
  > 函数定义string.format() 第一个参数为字符串格式，后面的参数可以任意多个， 用于填充第一个参数中的格式控制符，最后返回完整的格式化后的字符串。
  >格式控制符以%开头，常用的有以下几种：
  >* %s    -  接受一个字符串并按照给定的参数格式化该字符串
  >* %d    - 接受一个数字并将其转化为有符号的整数格式
  >* %f    -  接受一个数字并将其转化为浮点数格式(小数)，默认保留6位小数，不足位用0填充
  >* %x    - 接受一个数字并将其转化为小写的十六进制格式
  >* %X    - 接受一个数字并将其转化为大写的十六进制格式
  ><pre>
  str = string.format("字符串：%s\n整数：%d\n小数：%f\n十六进制数：%X","qweqwe",1,0.13,348)
print(str)
--[[ 输出结果：
字符串：qweqwe
整数：1
小数：0.130000
十六进制数：15C
]]</pre>


* select函数用于遍历变长参数时，存在nil的情况。调用select时，
必须传入一个固定实参selector（选择开关）和一系列变长参数。
如果selector为数字n，那么select返回他的第n个可变实参；否则，
selector只能为字符串“#”，这样select会返回变长参数的总数
<pre>--select遍历函数的所有变长参数，select会返回所有变长参数总数（包括nil）
for i=1, select('#', ...) do
local arg = select(i, ...)
  --循环体
end</pre>


#### 具名实参
<pre>--os.rename函数用于更改文件名，用old/new为实参命名代表改前和改后
rename{old="temp.lua", new="temp1.lua"}

--rename({old="temp.lua", new="temp1.lua"})
--由于实参是table可以省略括号</pre>

<pre>--假设一个创建新窗口的函数
w = Window{x=0, y=0, width=300, height=200,
                        title="Lua", background="blue",
                        border=true}

--window函数：
function Window(options)
  if type(options.title) ~= "string" then
    error("no titile")
  elseif type(options.width) ~= "number" then
    error("no width")
  elseif typle(options.height) ~= "number" then
    error("no height")
  end

--_window函数用于实际创建窗口
_Window(options.title,
                  options.x or 0,
                  options.y or 0,
                  options.width, options.height,
                  options.background or "white",
                  options.border
                  )
  end</pre>


---
### 函数深入
* 第一类值：Lua中函数与其他传统类型的值具有相同的权利。函数可以储存到变量/table中，
可以作为实参传递给其他函数，可以作为其他函数的返回值。
* 词法域：函数可以嵌套到另一个函数中，内部函数可以访问外部函数的变量。
* Lua中函数和其他所有值一样都是匿名的：
  <pre>
  a = {p = print}
  a.p("Hello World")    -->Hello World
  print = math.sin    -->'print'现在引用了正弦函数
  a.p(print(1))   -->0.841470
  sin = a.p   -->'sin'现在引用了print函数
  sin(10, 20)   -->10 20</pre>

* 高阶函数：接受另一个函数作为实参。
  * `table.sort()`排序操作：
    1. <a src="http://www.jb51.net/article/55718.htm">Lua中对table排序实例</a>
    2. <a src="http://www.jianshu.com/p/2f5bdc773f6d">Lua table.sort()</a>
<pre>function derivative(f, delta)
  delta = delta or 1e-4
  return function(x)
                return(f(x + delta) - f(x)) / delta
                end
end
--
c = derivative(math.sin)
print(math.cos(10), c(10))
--> -0.83907152907645   -0.83904432662041</p  re>

---
### 协同程序
