# pythonic
让python代码更高效

## 何为pythonic?
pythonic如果翻译成中文的话就是很python。很+名词结构的用法在中国不少，比如：很娘，很国足，很CCTV等等。

我的理解为，很+名词表达了一种特殊和强调的意味。所以很python可以理解为：只有python能做到的，区别于其他语言的写法，其实就是python的惯用和特有写法。

举个例子：

很python的写法：

	a,b = b,a
	
不python的写法：

	temp = a
	a = b
	b = temp
	
上面的例子通过了元组的pack和unpack完成了对a,b的互换，避免了使用临时变量temp，而且只用了一行代码。

以下为了简略，我们用P表示pythonic的写法，NP表示non-pythonic的写法，当然此P-NP非彼P-NP。


## 为什么要追求pythonic?

相比于NP，P的写法简练，明确，优雅，绝大部分时候执行效率高，代码越少也就越不容易出错。我认为好的程序员在写代码时，应该追求代码的正确性，简洁性和可读性，这恰恰就是pythonic的精神所在。

对于具有其他编程语言经验而初涉Python的程序员（比如我自己）来说，在写Python代码时，认识到pythonic的写法，会带来更多的便利和高效，而本文的主要读者也将是这群程序员。

以下将给出P和NP的n种示例，供读者和自己参考，查阅。

本文最后会列出参考资料，这些参考资料在我看来都极具价值。

# P vs. NP的示例

## 链式比较

P:

	1
	2
	3
	4
	a = 3
	b = 1 
 
	1 <= b <= a < 10  
	# True	
NP:

	b >= 1 and b <= a and a < 10 
	# True
P是小学生都能看懂的语法，简单直接省代码~


## 真值测试

P:
	
	name = 'Tim'
	langs = ['AS3', 'Lua', 'C']
	info = {'name': 'Tim', 'sex': 'Male', 'age':23 }    
 
	if name and langs and info:
    	print('All True!')  #All True!
NP:

	if name != '' and len(langs) > 0 and info != {}:
    	print('All True!') #All True!
简而言之，P的写法就是对于任意对象，直接判断其真假，无需写判断条件，这样既能保证正确性，又能减少代码量。

真假值表(记住了假你就能省很多代码！)

真             |           假
-------------  | -------------
True | False
任意非空字符串|空的字符串 ''
任意非0数字|	数字0
任意非空容器|	空的容器 [] () {} set()
其他任意非False|	None

## contains and iteration

P:

	name = 'Safe Hammad'
 	if 'H' in name:
 		print('This name has an H in it!') 
 		
 	pets = ['Dog', 'Cat', 'Hamster']
 	for pet in pets:
 		print('A', pet, 'can be very cute!')
 
NP:

	name = 'Safe Hammad'
 	if name.find('H') != -1:
 		print('This name has an H in it!')
	
	pets = ['Dog', 'Cat', 'Hamster']
 	i = 0
 	while i < len(pets):
 		print('A', pets[i], 'can be very cute!')
 		i += 1
 	
contains 可以用于 lists, dicts (keys), sets, strings
还可以用于已实现 __contains__ 方法的类


## 字符串反转

P:

	def reverse_str( s ):
    	return s[::-1] 
 
NP:

	def reverse_str( s ):
    	t = ''
    	for x in xrange(len(s)-1,-1,-1):
        	t += s[x]
    	return t
	
P的写法简单，经测试，效率也更好。
 
如果用于检测回文，就是一句话input == input[::-1]，多么的优雅！



## 字符串列表的连接

P:

	strList = ["Python", "is", "good"]  
 
	res =  ' '.join(strList) 
	# Python is good 
 
NP:

	res = ''
	for s in strList:
    	res += s + ' '
	# Python is good 
	# 最后还有个多余空格
	
string.join()常用于连接列表里的字符串，相对于NP，P的方式十分高效，且不会犯错。



## EAFP优于LBYL

EAFP: It's Easier to Ask for Forgiveness than
Permission.

LBYL: Look Before You Leap

P:

	d = {'x': '5'}
 	try:
 		value = int(d['x'])
 	except (KeyError, TypeError, ValueError):
 		value = None 
 
NP:

	d = {'x': '5'}
 	if 'x' in d and \
 		isinstance(d['x'], str) and \
 		d['x'].isdigit():
 		value = int(d['x'])
 	else:
 		value = None
	
python抛出异常并不像java那样代价非常昂贵


## 列表求和，最大值，最小值，乘积

P:

	numList = [1,2,3,4,5]   
 
	sum = sum(numList)  #sum = 15
	maxNum = max(numList) #maxNum = 5
	minNum = min(numList) #minNum = 1
	from operator import mul
	prod = reduce(mul, numList, 1) #prod = 120 默认值传1以防空列表报错

 
NP:

	sum = 0
	maxNum = -float('inf')
	minNum = float('inf')
	prod = 1
	for num in numList:
    	if num > maxNum:
        	maxNum = num
    	if num < minNum:
        	minNum = num
    	sum += num
    	prod *= num
	# sum = 15 maxNum = 5 minNum = 1 prod = 120
	
经简单测试，在numList的长度为10000000时，在我的机器上对列表求和，P耗时0.6s，NP耗时1.3s，将近两倍的差距。所以不要自己造轮子了。


## 列表推导式

P:

	l = [x*x for x in range(10) if x % 3 == 0]
	# l = [0, 9, 36, 81] 
 
NP:

	l = []
	for x in range(10):
    	if x % 3 == 0:
        	l.append(x*x)        	
	# l = [0, 9, 36, 81]	
	
你看，使用P的列表推导式，构建新列表变得多么简单直观！


## 字典的默认值

P:

	dic = {'name':'Tim', 'age':23}  
 
	dic['workage'] = dic.get('workage',0) + 1
	# dic = {'age': 23, 'workage': 1, 'name': 'Tim'}
 
NP:

	if 'workage' in dic:
    	dic['workage'] += 1
	else:
    	dic['workage'] = 1
	# dic = {'age': 23, 'workage': 1, 'name': 'Tim'}	
dict的get(key,default)方法用于获取字典中key的值，若不存在该key，则将key赋默认值default。
P相比NP的写法少了if...else...，实乃痛恨if...else...之人首选！


## for…else…语句

P:

	for x in xrange(1,5):
    	if x == 5:
        	print 'find 5'
        	break
	else:
    	print 'can not find 5!'
	# can not find 5!
 
NP:

	find = False
	for x in xrange(1,5):
    	if x == 5:
        	find = True
        	print 'find 5'
        	break
	if not find:
    	print 'can not find 5!'
	# can not find 5!	
	
for...else...的else部分用来处理没有从for循环中断的情况。有了它，我们不用设置状态变量来检查是否for循环有break出来，简单方便。


## 三元符的替代

P:

	a = 3   
 
	b = 2 if a > 2 else 1
	# b = 2
 
NP:

	if a > 2:
    	b = 2
	else:
    	b = 1
	# b = 2	
	
如果你具备C的编程经验，你就会寻找A ? B : C的替代品。你可能发现A and B or C看起来还不错，但是b = a > 1 and False or True会返回True，而实际意图应该返回False。 
使用b = False if a > 1 else True则会正确返回False，所以它才是正宗的三元符替代品。

## Enumerate

P:

	array = [1, 2, 3, 4, 5]
 
	for i, e in enumerate(array,0):
    	print i, e
	#0 1
	#1 2
	#2 3
	#3 4
	#4 5  
NP:

	for i in xrange(len(array)):
    	print i, array[i]
	#0 1
	#1 2
	#2 3
	#3 4
	#4 5 	
使用enumerate可以一次性将索引和值取出，避免使用索引来取值，而且enumerate的第二个参数可以调整索引下标的起始位置，默认为0。

## 使用zip创建键值对

P:

	keys = ['Name', 'Sex', 'Age']
	values = ['Tim', 'Male', 23]
 
	dic = dict(zip(keys, values))
	# {'Age': 23, 'Name': 'Tim', 'Sex': 'Male'}
NP:

	dic = {}
	for i,e in enumerate(keys):
    	dic[e] = values[i]
	# {'Age': 23, 'Name': 'Tim', 'Sex': 'Male'}

zip方法返回的是一个元组，用它来创建键值对，简单明了。

## 文件读取

P:

	file = open("some_filename", "r")

	while 1:   # infinite loop
    	line = file.readline()
    	if not line:  # 'readline()' returns None at end of file.
        	break

    # Process the line.
NP:

	file = open("some_filename", "r")

	for line in file:
    	# Process the line.



	
