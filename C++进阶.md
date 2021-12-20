一、内联函数inline
引入内联函数的目的是为了解决程序中函数调用的效率问题，这么说吧，程序在编译器编译的时候，编译器将程序中出现的内联函数的调用表达式用内联函数的函数体进行替换，
而对于其他的函数，都是在运行时候才被替代。这其实就是个空间代价换时间的i节省。所以内联函数一般都是1-5行的小函数。在使用内联函数时要留神：

1.在内联函数内不允许使用循环语句和开关语句；
2.内联函数的定义必须出现在内联函数第一次调用之前；
3.类结构中所在的类说明内部定义的函数是内联函数。

二、使用new和delete时，使用相同的形式
 在new中使用了[]，则在delete中也应该用[]
 在new中没有使用[]，则在delete中一定不要用[]。
  
三、转换构造函数
 1、 转换构造函数是将一个其他类型的数据转换成一个类的对象。
  转换构造函数只有一个形参，如
    Complex(double r) {real=r;imag=0;}
其作用是将double型的参数r转换成Complex类的对象，将r作为复数的实部，虚部为0。用户可以根据需要定义转换构造函数，在函数体中告诉编译系统怎样去进行转换。
  转换构造函数也是一种构造函数，它遵循构造函数的一般规则。通常把有一个参数的构造函数用作类型转换，所以，称为转换构造函数。

2、再谈构造函数
构造函数的本意是在创建对象的时候初始化对象，编译器会根据传递的实参来匹配不同的（重载的）构造函数。
1) 默认构造函数。就是编译器自动生成的构造函数。以 Complex 类为例，它的原型为：
Complex();  //没有参数

2) 普通构造函数。就是用户自定义的构造函数。以 Complex 类为例，它的原型为：
Complex(double real, double imag);  //两个参数

3) 拷贝构造函数。在以拷贝的方式初始化对象时调用。以 Complex 类为例，它的原型为：
Complex(const Complex &c);

4) 转换构造函数。将其它类型转换为当前类类型时调用。以 Complex 为例，它的原型为：
Complex(double real);

四、类型转换函数的语法格式为：
operator type(){
    //TODO:
    return data;
}

operator 是 C++ 关键字，type 是要转换的目标类型，data 是要返回的 type 类型的数据。
因为要转换的目标类型是 type，所以返回值 data 也必须是 type 类型。
#include <iostream>
using namespace std;

//复数类
class Complex{
public:
    Complex(): m_real(0.0), m_imag(0.0){ }
    Complex(double real, double imag): m_real(real), m_imag(imag){ }
public:
    friend ostream & operator<<(ostream &out, Complex &c);
    friend Complex operator+(const Complex &c1, const Complex &c2);
    operator double() const { return m_real; }  //类型转换函数
private:
    double m_real;  //实部
    double m_imag;  //虚部
};

//重载>>运算符
ostream & operator<<(ostream &out, Complex &c){
    out << c.m_real <<" + "<< c.m_imag <<"i";;
    return out;
}
//重载+运算符
Complex operator+(const Complex &c1, const Complex &c2){
    Complex c;
    c.m_real = c1.m_real + c2.m_real;
    c.m_imag = c1.m_imag + c2.m_imag;
    return c;
}

int main(){
    Complex c1(24.6, 100);
    double f = c1;  //相当于 double f = Complex::operator double(&c1);
    cout<<"f = "<<f<<endl;
 
    f = 12.5 + c1 + 6;  //相当于 f = 12.5 + Complex::operator double(&c1) + 6;
    cout<<"f = "<<f<<endl;
 
    int n = Complex(43.2, 9.3);  //先转换为 double，再转换为 int
    cout<<"n = "<<n<<endl;

    return 0;
}

运行结果：
f = 24.6
f = 43.1
n = 43

类型转换函数和运算符的重载非常相似，都使用 operator 关键字，因此也把类型转换函数称为类型转换运算符。

（一）、关于类型转换函数的说明

1) type 可以是内置类型、类类型以及由 typedef 定义的类型别名，任何可作为函数返回类型的类型（void 除外）都能够被支持。一般而言，不允许转换为数组或函数类型，转换为指针类型或引用类型是可以的。

2) 类型转换函数一般不会更改被转换的对象，所以通常被定义为 const 成员。

3) 类型转换函数可以被继承，可以是虚函数。

4) 一个类虽然可以有多个类型转换函数（类似于函数重载），但是如果多个类型转换函数要转换的目标类型本身又可以相互转换（类型相近），那么有时候就会产生二义性。

operator double() const { return m_real; }  //转换为double类型
operator int() const { return (int)m_real; }  //转换为int类型

那么下面的写法就会引发二义性：
Complex c1(24.6, 100);
float f = 12.5 + c1;

（二）、四种转换类型
 static_cast、dynamic_cast、const_cast和reinterpret_cast
    关键字	                   说明
static_cast    	      用于良性转换，一般不会导致意外发生，风险很低。
const_cast	           用于 const 与非 const、volatile 与非 volatile 之间的转换。
reinterpret_cast	     高度危险的转换，这种转换仅仅是对二进制位的重新解释，不会借助已有的转换规则对数据进行调整，但是可以实现最灵活的 C++ 类型转换。
dynamic_cast         	借助 RTTI，用于类型安全的向下转型（Downcasting）。

这四个关键字的语法格式都是一样的，具体为：
xxx_cast<newType>(data)

newType 是要转换成的新类型，data 是被转换的数据。例如，老式的C风格的 double 转 int 的写法为：
double scores = 95.5;
int n = (int)scores;
C++ 新风格的写法为：
double scores = 95.5;
int n = static_cast<int>(scores);

1、static_cast 
static_cast 只能用于良性转换，这样的转换风险较低，一般不会发生什么意外，例如：
原有的自动类型转换，例如 short 转 int、int 转 double、const 转非 const、向上转型等；
void 指针和具体类型指针之间的转换，例如void *转int *、char *转void *等；
有转换构造函数或者类型转换函数的类与其它类型之间的转换，例如 double 转 Complex（调用转换构造函数）、Complex 转 double（调用类型转换函数）。

需要注意的是，static_cast 不能用于无关类型之间的转换，因为这些转换都是有风险的，例如：
两个具体类型指针之间的转换，例如int *转double *、Student *转int *等。不同类型的数据存储格式不一样，长度也不一样，
  用 A 类型的指针指向 B 类型的数据后，会按照 A 类型的方式来处理数据：如果是读取操作，可能会得到一堆没有意义的值；
  如果是写入操作，可能会使 B 类型的数据遭到破坏，当再次以 B 类型的方式读取数据时会得到一堆没有意义的值。
int 和指针之间的转换。将一个具体的地址赋值给指针变量是非常危险的，因为该地址上的内存可能没有分配，也可能没有读写权限，恰好是可用内存反而是小概率事件。

2、const_cast
const_cast 比较好理解，它用来去掉表达式的 const 修饰或 volatile 修饰。换句话说，const_cast 就是用来将 const/volatile 类型转换为非 const/volatile 类型。
const int n = 100;
int *p = const_cast<int*>(&n);
*p = 234;
&n用来获取 n 的地址，它的类型为const int *，必须使用 const_cast 转换为int *类型后才能赋值给 p。由于 p 指向了 n，并且 n 占用的是栈内存，有写入权限，所以可以通过 p 修改 n 的值。
   cout<<"n = "<<n<<endl;
    cout<<"*p = "<<*p<<endl;
运行结果：
n = 100
*p = 234
 
 n 和 *p 输出的值不一样，C++ 对常量的处理更像是编译时期的#define，是一个值替换的过程，代码中所有使用 n 的地方在编译期间就被替换成了 100。换句话说，第 8 行代码被修改成了下面的形式：
cout<<"n = "<<100<<endl;
这样以来，即使程序在运行期间修改 n 的值，也不会影响 cout 语句了。

3、reinterpret 
是“重新解释”的意思，顾名思义，reinterpret_cast 这种转换仅仅是对二进制位的重新解释，不会借助已有的转换规则对数据进行调整，非常简单粗暴，所以风险很高。
reinterpret_cast 可以认为是 static_cast 的一种补充，一些 static_cast 不能完成的转换，就可以用 reinterpret_cast 来完成，
例如两个具体类型指针之间的转换、int 和指针之间的转换（有些编译器只允许 int 转指针，不允许反过来）。
#include <iostream>
using namespace std;
class A{
public:
    A(int a = 0, int b = 0): m_a(a), m_b(b){}
private:
    int m_a;
    int m_b;
};
int main(){
    //将 char* 转换为 float*
    char str[]="http://c.biancheng.net";
    float *p1 = reinterpret_cast<float*>(str);
    cout<<*p1<<endl;
    //将 int 转换为 int*
    int *p = reinterpret_cast<int*>(100);
    //将 A* 转换为 int*
    p = reinterpret_cast<int*>(new A(25, 96));
    cout<<*p<<endl;
   
    return 0;
}

运行结果：
3.0262e+29
25
4、dynamic_cast 关键字
dynamic_cast 用于在类的继承层次之间进行类型转换，它既允许向上转型（Upcasting），也允许向下转型（Downcasting）。向上转型是无条件的，不会进行任何检测，所以都能成功；
向下转型的前提必须是安全的，要借助 RTTI 进行检测，所有只有一部分能成功。

dynamic_cast 与 static_cast 是相对的，dynamic_cast 是“动态转换”的意思，static_cast 是“静态转换”的意思。
dynamic_cast 会在程序运行期间借助 RTTI 进行类型转换，这就要求基类必须包含虚函数；static_cast 在编译期间完成类型转换，能够更加及时地发现错误。

dynamic_cast 的语法格式为：
dynamic_cast <newType> (expression)

newType 和 expression 必须同时是指针类型或者引用类型。换句话说，dynamic_cast 只能转换指针类型和引用类型，其它类型（int、double、数组、类、结构体等）都不行。

对于指针，如果转换失败将返回 NULL；对于引用，如果转换失败将抛出std::bad_cast异常。
1) 向上转型（Upcasting）
向上转型时，只要待转换的两个类型之间存在继承关系，并且基类包含了虚函数（这些信息在编译期间就能确定），
就一定能转换成功。因为向上转型始终是安全的，所以 dynamic_cast 不会进行任何运行期间的检查，这个时候的 dynamic_cast 和 static_cast 就没有什么区别了。
 //情况①
    Derived *pd1 = new Derived(35, 78);
    Base *pb1 = dynamic_cast<Derived*>(pd1);
    cout<<"pd1 = "<<pd1<<", pb1 = "<<pb1<<endl;
    cout<<pb1->get_a()<<endl;
    pb1->func();
    //情况②
    int n = 100;
    Derived *pd2 = reinterpret_cast<Derived*>(&n);
    Base *pb2 = dynamic_cast<Base*>(pd2);
    cout<<"pd2 = "<<pd2<<", pb2 = "<<pb2<<endl;
    cout<<pb2->get_a()<<endl;  //输出一个垃圾值
    pb2->func();  //内存错误
    情况①是正确的，没有任何问题。对于情况②，pd 指向的是整型变量 n，并没有指向一个 Derived 类的对象，在使用 dynamic_cast 进行类型转换时也没有检查这一点，而是将 pd 的值直接赋给了 pb（这里并不需要调整偏移量），最终导致 pb 也指向了 n。因为 pb 指向的不是一个对象，所以get_a()得不到 m_a 的值（实际上得到的是一个垃圾值），pb2->func()也得不到 func() 函数的正确地址。
pb2->func()得不到 func() 的正确地址的原因在于，pb2 指向的是一个假的“对象”，它没有虚函数表，也没有虚函数表指针，而 func() 是虚函数，必须到虚函数表中才能找到它的地址。
2) 向下转型（Downcasting）
向下转型是有风险的，dynamic_cast 会借助 RTTI 信息进行检测，确定安全的才能转换成功，否则就转换失败。
dynamic_cast 会在程序运行过程中遍历继承链，如果途中遇到了要转换的目标类型，那么就能够转换成功，如果直到继承链的顶点（最顶层的基类）还没有遇到要转换的目标类型，那么就转换失败。
但是从本质上讲，dynamic_cast 还是只允许向上转型，因为它只会向上遍历继承链。造成这种假象的根本原因在于，派生类对象可以用任何一个基类的指针指向它，这样做始终是安全的。

四、C++输入流和输出流
ios 是所有流类的基类，它派生出 istream 和 ostream。特别需要指出的是，为了避免多继承的二义性，从 ios 派生出 istream 和 ostream 时，均使用了 virtual 关键字（虚继承）。
这些流类各自的功能分别为：
istream：常用于接收从键盘输入的数据；
ostream：常用于将数据输出到屏幕上；
ifstream：用于读取文件中的数据；
ofstream：用于向文件中写入数据；
iostream：继承自 istream 和 ostream 类，因为该类的功能兼两者于一身，既能用于输入，也能用于输出；
fstream：兼 ifstream 和 ofstream 类功能于一身，既能读取文件中的数据，又能向文件中写入数据。

cout、cerr 和 clog 之间的区别如下：
cout 除了可以将数据输出到屏幕上，通过重定向（后续会讲），还可以实现将数据输出到指定文件中；而 cerr 和 clog 都不支持重定向，它们只能将数据输出到屏幕上；
cout 和 clog 都设有缓冲区，即它们在输出数据时，会先将要数据放到缓冲区，等缓冲区满或者手动换行（使用换行符 '\n' 或者 endl）时，才会将数据全部显示到屏幕上；而 cerr 则不设缓冲区，它会直接将数据输出到屏幕上。
成员方法名	功能
getline(str,n,ch)	    从输入流中接收 n-1 个字符给 str 变量，当遇到指定 ch 字符时会停止读取，默认情况下 ch 为 '\0'。
get()	                从输入流中读取一个字符，同时该字符会从输入流中消失。
gcount() 	            返回上次从输入流提取出的字符个数，该函数常和 get()、getline()、ignore()、peek()、read()、readsome()、putback() 和 unget() 联用。
peek()               	返回输入流中的第一个字符，但并不是提取该字符。
putback(c) 	          将字符 c 置入输入流（缓冲区）。
ignore(n,ch)	         从输入流中逐个提取字符，但提取出的字符被忽略，不被使用，直至提取出 n 个字符，或者当前读取的字符为 ch。
operator>>	           重载 >> 运算符，用于读取指定类型的数据，并返回输入流对象本身。

成员方法名	      功能
put()	      输出单个字符。
write()	    输出指定的字符串。
tellp()	    用于获取当前输出流指针的位置。
seekp()	    设置输出流指针的位置。
flush()	    刷新输出流缓冲区。
operator<<	 重载 << 运算符，使其用于输出其后指定类型的数据。

write() 成员方法专用于向输出流缓冲区中添加指定的字符串。其语法格式如下：
ostream＆write（const char * s，streamsize n）;
s 用于指定某个长度至少为 n 的字符数组或字符串；n 表示要输出的前 n 个字符。

tellp() 成员方法用于获取当前输出流缓冲区中最后一个字符所在的位置，其语法格式如下：
streampos tellp();

显然，tellp() 不需要传递任何参数，会返回一个 streampos 类型值。
事实上，streampos 是 fpos 类型的别名，而 fpos 通过自动类型转换，可以直接赋值给一个整形变量（即 short、int 和 long）。
也就是说，在使用此函数时，我们可以用一个整形变量来接收该函数的返回值。
当输出流缓冲区中没有任何数据时，该函数返回的整形值为 0；当指定的输出流缓冲区不支持此操作，或者操作失败时，该函数返回的整形值为 -1。

C++ seekp()成员方法
seekp() 方法用于指定下一个进入输出缓冲区的字符所在的位置。

seekp() 方法有如下 2 种语法格式：
//指定下一个字符存储的位置
ostream& seekp (streampos pos);
//通过偏移量间接指定下一个字符的存储位置   
ostream& seekp (streamoff off, ios_base::seekdir way);
其中，各个参数的含义如下：
pos：用于接收一个正整数；
off：用于指定相对于 way 位置的偏移量，其本质也是接收一个整数，可以是正数（代表正偏移）或者负数（代表负偏移）；
way：用于指定偏移位置，即从哪里计算偏移量，它可以接收表 1 所示的 3 个值。

表 1 文件定位标志
模式标志	描 述
ios::beg	从文件头开始计算偏移量
ios::end	从文件末尾开始计算偏移量
ios::cur	从当前位置开始计算偏移量

四、多态与虚函数

1、RTT机制
 1）、RTTI
 RTTI是Runtime Type Identification的缩写，意思是运行时类型识别。

 C++引入这个机制是为了让程序在运行时能根据基类的指针或引用来获得该指针或引用所指的对象的实际类型。
 但是现在RTTI的类型识别已经不限于此了，它还能通过typeid操作符识别出所有的基本类型（int，指针等）的变量对应的类型。

 C++通过以下的两个操作提供RTTI：

 （1）typeid运算符，该运算符返回其表达式或类型名的实际类型。

 （2）dynamic_cast运算符，该运算符将基类的指针或引用安全地转换为派生类类型的指针或引用。

 RTTI必须建立在虚函数的基础上，否则无需RTTI技术

 RTTI技术可以通过父类指针识别其所指向对象的真实数据类型

1、typeid运算符
  typeid运算符，后接一个类型名或一个表达式，该运算符返回一个类型为std::tpeinf的对象的const引用。

  typeid注意事项：

  type_id返回一个type_info对象的引用
  如果想通过基类的指针获得派生类的数据类型，基类必须带有虚函数
  只能获取对象的实际类型

  typeid 运算符用来获取一个表达式的类型信息。类型信息对于编程语言非常重要，它描述了数据的各种属性：
  对于基本类型（int、float 等C++内置类型）的数据，类型信息所包含的内容比较简单，主要是指数据的类型。
  对于类类型的数据（也就是对象），类型信息是指对象所属的类、所包含的成员、所在的继承关系等。

  类型信息是创建数据的模板，数据占用多大内存、能进行什么样的操作、该如何操作等，这些都由它的类型信息决定。

  typeid 的操作对象既可以是表达式，也可以是数据类型，下面是它的两种使用方法：
  typeid( dataType )
  typeid( expression )

  dataType 是数据类型，expression 是表达式，这和 sizeof 运算符非常类似，只不过 sizeof 有时候可以省略括号( )，而 typeid 必须带上括号。
  name() 用来返回类型的名称。
  raw_name() 用来返回名字编码（Name Mangling）算法产生的新名称。关于名字编码的概念，我们已在《C++函数编译原理和成员函数的实现》中讲到。
  hash_code() 用来返回当前类型对应的 hash 值。hash 值是一个可以用来标志当前类型的整数，有点类似学生的学号、公民的身份证号、银行卡号等。
  不过 hash 值有赖于编译器的实现，在不同的编译器下可能会有不同的整数，但它们都能唯一地标识某个类型。

  C++ 标准规定，type_info 类至少要有如下所示的 4 个 public 属性的成员函数，其他的扩展函数编译器开发者可以自由发挥，不做限制。
  1) 原型：const char* name() const;
  返回一个能表示类型名称的字符串。但是C++标准并没有规定这个字符串是什么形式的，例如对于上面的objInfo.name()语句，VC/VS 下返回“class Base”，但 GCC 下返回“4Base”。
  2) 原型：bool before (const type_info& rhs) const;
  判断一个类型是否位于另一个类型的前面，rhs 参数是一个 type_info 对象的引用。但是C++标准并没有规定类型的排列顺序，不同的编译器有不同的排列规则，程序员也可以自定义。要特别注意的是，这个排列顺序和继承顺序没有关系，基类并不一定位于派生类的前面。
  3) 原型：bool operator== (const type_info& rhs) const;
  重载运算符“==”，判断两个类型是否相同，rhs 参数是一个 type_info 对象的引用。
  4) 原型：bool operator!= (const type_info& rhs) const;
  重载运算符“!=”，判断两个类型是否不同，rhs 参数是一个 type_info 对象的引用。
2、dynamic_cast运算符
  把一个基类类型的指针或引用转换至继承架构的末端某一个派生类类型的指针或引用被称为向下转型（downcast）。dynamic_cast运算符的作用是安全而有效地进行向下转型。

  dynamic_cast注意事项：

  只能应用于指针和引用的转换
  要转换的类型中必须包含虚函数
  转换成功返回子类的地址，失败返回NULL








