#### STL容器

STL大体分为六大组件，分别是:容器、算法、迭代器、仿函数、适配器（配接器）、空间配置器

stl 序列式容器无大小排序，关联式容器有。



算法分为:**质变算法**和**非质变算法**。

质变算法会更改元素内容，非质变算法不会。



每个容器都有自己专属的迭代器，迭代器类似于指针。

常用的容器中迭代器种类为双向迭代器（能读能写，能向前和向后操作），和随机访问迭代器（以跳跃的方式访问任意数据）。



##### **string**

string本质上是一个类，类内部封装了char\*，管理这个字符串，是一个char*型的容器。

`string(int n, char c);`           //使用n个字符c初始化 

`string& assign(int n, char c);`                  //用n个字符c赋给当前字符串

用`append()`实现字符拼接，`string& append(const string &s, int pos, int n);`//字符串s中从pos开始的n个字符连接到字符串结尾



##### **vector**

vector可看做一个数组,但vector可以动态扩展

vector只能尾插和尾删

v.end()指向容器中最后一个元素的下一个

建立迭代器：`vector<int>::iterator itBegin = v.begin();`

stl提供的遍历算法：`for_each(v.begin(), v.end(), MyPrint);`MyPrint为一个函数（使用遍历算法前要先包含头文件`#include <algorithm>`）

for循环遍历迭代器：`for (vector<int>::iterator it = v.begin(); it != v.end(); it++) `

`                                      {
		                 cout << *it << endl;
	               }`

`*it`相当于取值操作

尾插  --- push_back；尾删  --- pop_back；插入  --- insert    (位置迭代器)；删除  --- erase  （位置迭代器）；清空  ---  clear  

`swap(vec);`  // 将vec与本身的元素互换;

`vector<int>(v).swap(v)`分析：vector<int>(v)是一个匿名对象，按照原有的v来初始化这个匿名对象，假如v的size为3，则这个对象的size和capacity也为3，之后的.swap(v)将这个匿名对象和v交换，<u>达到当v的capacity远大于size时收缩v内存的目的</u>

vector在动态扩展容量时的扩展次数：

```c++
int num;
int* p = NULL;

while (p != &v[0])
{
	p = &v[0];
	num++;
}
```

​       用reserve()预留空间可以减少扩展内存的次数

##### deque

deque容器为双端数组，内部含有中控器，且能达到头尾端都可插入的效果

![image-20211114151151194](C:\Users\GrandeLai\AppData\Roaming\Typora\typora-user-images\image-20211114151151194.png)

​     

deque容器的迭代器也是支持随机访问的，将容器改为只读不写可以这样在参数里加const和在迭代器iterator前加const，这样*it=100就会报错

```c++
`void printDeque(const deque<int>& d) 
{
	for (deque<int>::const_iterator it = d.begin(); it != d.end(); it++) {
		cout << *it << " ";
}`
```

deque容器没有容量的概念

`insert(pos,beg,end);`    //在pos位置插入[beg,end)区间的数据，无返回值。beg可以为另一个容器的begin(),end为另一个容器的end(),如`d.insert(d.begin(), d2.begin(), d2.end());`

插入和删除提供的位置是迭代器！删除某一位置的数据：

`deque<int>::iterator it = d.begin();
	it += 2;
	d.erase(it);`

利用算法实现对deque容器进行排序：`sort(iterator beg, iterator end)`  //对beg和end区间内元素进行排序

对于支持随机访问的迭代器的容器都能通过sort()进行排序

也要先	#include <algorithm>



##### **stack 栈**

只有栈顶可以出入，先进后出，栈顶元素叫top元素，栈不允许有遍历行为，只有栈顶元素才能被访问到



##### queue 队列

先进先出，两个出口，队头front()出队队尾back()入队，也不允许遍历，但是可以访问查看队尾和对头元素



##### list 链表

stl中的链表是双向链表，指针域由指向后一个和前一个两个指针组成，

![image-20211116090107579](C:\Users\GrandeLai\AppData\Roaming\Typora\typora-user-images\image-20211116090107579.png)

list的迭代器是双向迭代器，不支持跳跃式访问，插入操作和删除操作都不会造成原有list迭代器的失效，这在vector是不成立的。

`swap(lst);`                         //将lst与本身的元素互换。

`remove(elem);`//删除容器中所有与elem值匹配的元素。

list容器不能用中括号方式访问其中某一个元素，也不能用at()来访问，迭代器不支持随机访问，如：

`list<int>::iterator it = L1.begin();`

it++；可以，但是it=it+1；会报错

`reverse();`   //反转链表,因为list容器不支持迭代器随机访问所以其内部会提供一些算法，比如sort算法，但是这里的sort算法与前面容器的sort算法不同，这里的是成员函数，默认为升序，若要降序，需要指定一个排序规则，如在sort的参数列表里输入一个compare的函数，返回值为bool：

`bool myCompare(int t1, int t2)`
`{`
	`return t1 < t2;`
`}`
`L1.sort(myCompare);`



因为sort函数默认为升序，所以要改为升序也可以把<改为>

#####  set/ multiset 集合容器

元素在插入时会被自动排序，

* set不允许容器中有重复的元素
* multiset允许容器中有重复的元素

set容器插入只有insert的方式，若在set中插入了相同的元素，遍历时也不会显示

* `find(key);`                  //查找key是否存在,若存在，返回该键的元素的迭代器；若不存在，返回set.end();
* `count(key);`                //统计key的元素个数

###### pair对组

pair数组可以返回两个数据

**两种创建方式：**

* `pair<type, type> p ( value1, value2 );`

* `pair<type, type> p = make_pair( value1, value2 );`

* 如：pair<string,int>p("张三"，5)；

  获取pair中数据的方式：p.first，表示获取p数组中第一个数据；p.second表示获取p的第二个数据



set数组自定义排序：

在set容器建立时就利用仿函数进行排序方式改变，如：

`class myCompare`
`{`
`public:`
	`bool operator()(int v1,int v2)`
	`{`
		`return v1 > v2;`
	`}`
`};`

`void test01()`
`{`
	`set<int,myCompare> L;`

`}`

##### map/multimap容器

* map中所有元素都是pair
* pair中第一个元素为key（键值），起到索引作用，第二个元素为value（实值）
* 所有元素都会根据元素的键值自动排序

是关联式容器，能自动进行大小排序

* map不允许容器中有重复的key值
* multimap允许容器中有重复的key值

map和multimap都允许有相同的value值

`erase(key);`            //删除容器中值为key的元素，将值为key的一整个pair删掉

如：erase(3)表示删掉key为3的pair

四种插入方式：

`//第一种插入方式
	m.insert(pair<int, int>(1, 10));
	//第二种插入方式
	m.insert(make_pair(2, 20));
	//第三种插入方式
	m.insert(map<int, int>::value_type(3, 30));
	//第四种插入方式
	m[4] = 40; //但是如果没有找到m[4],会给你创建一个对组m[4],且它的value值默认会设为0，但是可以用这种方法通过key来访问到value的值`

#### STL对象

函数对象本质上是一个对象，只不过类似于一个函数，称为仿函数，重载了()，使用重载的()时，类似于函数调用

* 函数对象在使用时，可以像普通函数那样调用, 可以有参数，可以有返回值
* 函数对象超出普通函数的概念，函数对象可以有自己的状态
* 函数对象可以作为参数传递

函数对象可以有自己的状态,如利用函数对象中的成员记录函数调用次数，如：

`class Myprint
{
public:
	Myprint()
	{
		int count = 0;
	}
	void operator()(string s)
	{
		cout << s << endl;
		count++;
	}
	int count;
};`

函数对象可以作为参数进行传递：

`void doPrint(MyPrint &mp , string test)
{
	mp(test);
}`

`void test03()
{
	MyPrint myPrint;
	doPrint(myPrint, "Hello C++");
}`

