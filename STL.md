前述：  
   STL分为六大组件：容器、算法、迭代器、仿函数、适配器（配接器）、空间配置器；  
   1、迭代器：扮演了容器和算法之间的胶合剂;  
   2、仿函数：行为类似函数，可作为算法的某种策略；  
   3、空间配置器：负责空间的配置与管理;  

  所有不支持随机访问迭代器的容器，不可以用标准的算法，如：sort(L1.begin(),L2.end())  
 A、序列式    
(1)容器：  
一、vector  
   1、创建vector模板类，要包含头文件vector，还要用<type>来表明对象的类型，以及动态分配内存，指明内存所需的矢量个数；  
      向量（Vector）是一个封装了动态大小数组的顺序容器（Sequence Container）。  
      顺序容器中的元素按照严格的线性顺序排序。可以通过元素在序列中的位置访问对应的元素。  
2.动态数组  
include<vector>
vector<int>Ratings(5);//创建int型的vector对象Ratings，里面的容量为5个时间复杂度是线性的
   2、其他成员函数
size()--返回容器内的元素个数
swap()--交换两个容器的内容，作用:可以压缩内存空间，假设v原来的内存空间为100，大小为3，
        当使用vector<int>(v).swap(v)时，v的大小和容量都是一样的，为3；
        vector<int>(v)为匿名对象,指向v原来的空间，当这行代码执行完之后，系统会回收匿名对象指向的空间  
begin()--返回指向容器中第一个元素的迭代器
end()--返回一个表示超过容器尾的迭代器
erase()--删除矢量中给定区间的元素，因此接受两个迭代器的参数   
insert()--与erase()的相反，但接受三个迭代器的参数，第一个是指定新元素的插入位置，后两个指定是插入位置的区间
push_back(val)--在内存空间尾部插入元素val
pop_back()--数据尾部进行删除数据
reserve(int len)--预留len个空间，使用在数据比较大时;

   3、其他操作函数   
   for_each(迭代器1，迭代器2，函数(输出函数名));//该函数可以在迭代器1和迭代器2之间输出容器的内容  
   sort(迭代器1，迭代器2);//该函数可以按两个迭代器之间的元素进行排序，但必须要重载符号函数，operator函数,
或：sort(迭代器1，迭代器2，函数);//排序根据函数的参数类型来进行，如果没有这个函数，则默认调用operator函数  
   random_shuffle(迭代器1，迭代器2);// 随机排序  
   4、当原内存的空间不够时，会新开辟比原来大两倍的空间  
   5、容器嵌套：vector<vector<int>>v;//相当于二维数组，赋值时先对内进行赋值，再对外进行赋值，用push_back()；
   输出时：两个for循环；  

```
   for (vector<vector<int>>::iterator It = v.begin(); It != v.end(); It++)
		{
			//（*It）是容器 vector<int>
			for (vector<int>::iterator it = (*It).begin(); it != (*It).end(); it++)
			{
				//因为是内嵌容器，因此先遍历内容器，则是由容器（*It）指向
				cout << (*it) << " ";
			}
			cout << endl;
		}
```

 缺点：头部插入删除效率低，数据量越大，效率越低。  
二、stack 栈   
  简概：栈是一种”先进后出“的数据结构，只有一个出口，用push进行压栈和pop进行出栈  
在C++中使用栈，需要包含头文件 stack,同时还要包含使用哪种容器作为底层容器。不允许遍历，不允许随机访问栈  
例如：使用list作为底层容器  
#include<list>
#include<stack>
....


使用：
  stack<int,list<int>>istack;//
  istack.push(元素)；//压栈
  istack.pop();//出栈，删除栈顶的元素
  istack.top();//将栈顶输出
  istack.size();//求出栈的大小
  bool.empty()const;//判空
相比C中，使用STL的栈更加方便，程序更加简便  

 三、deque
  是双端队列(或者数组),内部含有中控器，用来管理缓冲区的地址，缓冲区用来存放真实的数据；
  deque可以随机访问。
  deque的构造方式跟vector差不多
  其他操作函数也差不多，另外：
  deque.empty()//判空
  deque.size()//vector也有，返回容器中元素的大小
  deque.resize(num)//重新指定容器的大小为num
  deque.resize(num，elem)//重新指定容器的大小为num,若容器变长，则以num值填充新位置，若变小，则删除末尾的元素
  push_back()--尾插
  push_front()--头部插
  pop_back()--删除容器最后一个数据
  pop_front()--删除第一个数据

  insert(pos,elem);
  insert(pos,n,elem);
  insert(pos,begin(),end());在pos位置插入区间[begin,end)的数据

  AAAA级别案例：

```
void Mark(vector<person>&v)
{
	//int Score=0;
	//deque<int>d;//不能在这里声明，因为每次调用Mark函数时，d都会重新开辟空间，而原来的数据没有释放
	              //新的数据会在原来的空间上继续存放，所以会导致，每个选手的打分次数不一样
	//cout << "分别输入5名选手的分数： " << endl;
	for (vector <person>::iterator vt = v.begin(); vt != v.end(); vt++)
	{
		deque<int>d;//必须在这里声明，这样p才会重新开辟空间属于vt当前所指向的选手
		cout << vt->m_Name << " 打分： " << endl;
		for(int i=0;i<10;i++)
		{ 
			//cin >> Score;
			int mScore = rand() % 41 + 60;//60~100
			d.push_back(mScore);
		}
		//测试代码
                for (deque<int>::iterator de = d.begin(); de!= d.end(); de++)
		{
			cout<<*de<<" ";
		}
		cout << endl;

​	sort(d.begin(),d.end());//排序
​	int num = 0;
​	d.pop_back();//去掉最高分
​	d.pop_front();//去掉最低分
​	//不用删除函数
​            //d.erase(d.begin());
​	//d.erase(d.end());
​	for (deque<int>::iterator de = d.begin(); de != d.end(); de++)
​	{
​		num += *de;
​	}
​	//Score = num / mem;
​	int Score = num / d.size();
​	vt->m_Score = Score;//可以用容器直接访问类中的成员，因为vt就是vector<person>，因此可以截取到m_Score进行赋值
​	
}

}
```

  copy()函数： copy (myints, myints+7, myvector.begin());
              //myints是数组myints的第一个元素，myints+7是最后一个元素，将之间的元素放在vector定义的容器：myvector中

 四、list
   list 是一个双向链表，具备前移、后移的能力，在插入操作、接合操作都不会造成原有的list失效;拥有链表的优缺点：优：可以快速插入和删除，内存动态分配，缺：速度慢，空间大
在递增、递减、取值、成员取用操作时，递增时指向下一个节点，递减时指向上一个节点，取值时取的是节点的数值，成员取用时取用的是节点的成员;
不支持随机访问;
list的操作有：
front();//返回头元素
back();//返回尾元素
push_front(),
push_back(),
erase(),//删除具体的某个元素
pop_front(),
pop_back(),//出列，从尾
clear(),//清除所有元素
remove(elem),//一个参数，移除所有与elem相同的元素
unique(),//在连续相同的元素时，只能出现一次，即合为单个
splice()//三个参数，接合
merge()//单个参数，合并
reverse(),//逆序,无参
sort()//无参数 排序,默认升序
//逆序

```
bool Mycompare(int v1,int v2)
{
	return v1 > v2;
}
```

S1.sort(Mycompare);//传入一个bool型的函数，底层代码就会知道是逆排
//当要比较几个变量时，其中一个想等时，需要以另一个作为比较

```
bool Compare(person &p1, person & p2)
{
	if (p1.age == p2.age)
	{
		return p1.height > p2.height;
	}
	else
	{
		return p1.age > p2.age;
	}
}
```

L.sort(Compare);//当两个成员中，在其中一个相等的情况下，要以另一个来作为比较


transfer()//三个参数  迁移
resize();//重新定义容器的大小;如果空间比原来的大，则多出的默认值是0；或者是指定的数据；若比原来的小，则后面的数据将会被丢弃

五、queue
  队列容器，含有队头和队尾两个接口，数据只能从队头出，队尾只能进数据;
 队头队尾只能被外界访问，因此不能被遍历访问，如：排队时的先进先出;
 提供的接口：
 push(elem);//队尾添加元素
 pop();//队头移除第一个元素
 back();//返回最后一个元素
 front();//返回第一个元素
 empty();//判空
 size();//返回栈的大小
 使用时：
 如果是一个类的类型，则只将类压入队列中即可；其他接口使用方法不变；

 B、关联式
 关联式容器是将键值联系在一起，并通过键值来查询元素.
 find()函数返回的是迭代器
 一、set
 1、set中所有的元素会自动排序，而且不允许有相同的元素，只能出现一次，因此每个元素出现的个数都是1；其键值和实值是一样的
 插入数据不用push而是用insert；
 用count(elem)来算出元素为elem的个数;
 也有交换swap函数，删除函数erase，
 ...
 2、set 和multiset的区别：set在插入数据时，对于插入重复的数据会返回一个false的值，
 如：
 set<int>s;
 pair<set<int>::iterator,bool> ret=s.insert(10);
 pair表示对组，即成队出现;
 使用：pair<string,int>p(string("Chen")20);//或者pair<string,int>p=make_pair("Chen",20);
 cout<<"姓名："<<p.first<<"年龄："<<p.second<<endl;
 而multiset不管重复插入多少次都不会报错，而且会插入成功；使用底层机制RB_tree的insert_equal()，而不是set用的insert_unique()；
 multiset的用法跟set完全相同;
3、改变set的排序
不管数据类型是内置指定的，还是自定义的，都需要用到仿函数来实现改变排序的顺序，自定义类型必须要用仿函数来实现
内置指定类型：

```
class compare
{
public:
	bool operator()(int v1,int v2)//重载()//类型要看set设定的类型，保持一致
	{
		return v1 > v2;
	}

};
```

使用：

```
void changeSort()
{
	set<int, compare>st2;
	//在插入之前，先定义好排序的顺序，用仿函数来实现
	st2.insert(10);
	st2.insert(20);
	st2.insert(50);
	st2.insert(30);
	for (set<int, compare>::iterator iset = st2.begin(); iset != st2.end(); iset++)
	{
		cout << (*iset) << " ";
	}
	cout << endl;

}
```

自定义类型：

```
class Compare  
{
public:
	bool operator()(const person&p1, const person&p2)  //重载()
	{
		return p1.age > p2.age;
	}
};
```

使用：

```
void changeSort2()
{
	set<person, Compare>s;
	person p1("张飞",35);
	person p2("刘备", 45);
	person p3("关羽", 55);
	person p4("曹操", 25);
	s.insert(p1);
	s.insert(p2);
	s.insert(p3);
	s.insert(p4);

for (set<person, Compare>::iterator iset = s.begin(); iset != s.end(); iset++)
{
	cout <<"姓名：" <<(*iset).name<<" 年龄 ：" <<(*iset).age<< endl;
}	

}
```

二、map
   1、map容器元素根据键值自动排序，而且所有元素都是pair，pair中的第一个元素为key(键值)，唯一，起到索引的作用，第二个是value(实值)；
   同时，map也不可以重复插入，不可以修改map的键值；
   插入值需要用到pair来进行，
   pair<int, int>p1(2, 40);
   m1.insert(p1);
   或：m1.insert(make_pair(7,70));
   //查找

```
   map<int, int>::iterator ite = m1.find(7);
	if (ite != m1.end())
	{
		cout << "找到元素：键值为：" << (*ite).first << " 实值：" << ite->second << endl;
	}
	else
	{
		cout << "找不到元素：" << endl;
	}
	//计数
	int num = m1.count(7);
```

​    2、排序
​    改变map默认的排序顺序，则需要用到仿函数

```
  class Compare
	{
	public:
		bool operator()(int v1,int v2)
		{
			return v1 > v2;
		}
	};
    map<int, int, Compare>m1;//Compare是仿函数
    for (map<int, int,Compare>::iterator im = m1.begin(); im != m1.end(); im++)
	{
		cout << " 第一个值：--键值 ：" << (*im).first << " 第二个值：--实值：" << im->second << endl;
	}
```

