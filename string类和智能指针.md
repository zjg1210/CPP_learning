一、string类
   string类是由头文件string和cstring提供支持的，其中包含了多种构造函数；
 如下：
   string(const char *s);//s指向NBTS(空字符结束的字符串)
   string(size_type n,char c);//包含n个元素的string对象，都被初始化为字符c
   string(const string &str);
   string();//长度为0
   string(const char*s,size_type n);//将S指向NBTs的前n个字符
   template<class Iter>
   string(Iter begin,Iter end);//将string对象初始化为区间[begin,end)内的字符，不包括end,begin和end相当于指针
   string(const string &str,string size_type pos=0,size_type n=npos);//初始化为str中从位置pos到结尾的字符

   数组相当于指针，在上面第10行中如下，string str(alls+6,alls+10);//alls是一个string的数组或者字符串，
   该语句表示alls中第6个到第10个之间的字符串，不包括第10个
   string 也是一个容器
二、智能指针
     1、智能指针是用来管理指针的，管理指针的内存堆区，将普通指针封装成一个栈对象，等栈执行周期结束后，
   会自动调用对象的析构函数中自动释放内存，从而防止内存泄露的问题；智能指针是一个类，只有超出了类的作用域，才会自动调用析构函数

   使用智能指针，必须要包含memory头文件

 2、种类  
 智能指针中总共有四种（主要有三种），分别是：auto_ptr//在C++11中已弃用、share_ptr//共享拥有指针、unique_ptr//独享拥有指针

   weak_ptr//弱拥有指针，主要是填补share_ptr的问题（当两个指针互引用时，存在指针循环的问题，导致不能完全释放内存）；
   其中：1、auto_ptr：会造成内存崩溃  
         auto_ptr<string> p1 (new string ("I reigned lonely as a cloud.")); 
         auto_ptr<string> p2; 
         p2 = p1; //auto_ptr编译不会报错.运行时会报错  
       2、unique_ptr：实现独占式拥有或严格拥有概念，在同一时间，一个智能指针只能指向一个对象；避免内存泄露（忘记delete时）  
          auto_ptr<string> p1 (new string ("I reigned lonely as a cloud.")); 
          auto_ptr<string> p2; 
          p2 = p1; //auto_ptr会报错，想要实现，需要调用move(),转移p1的拥有权,p2=move(p1)  
       3、shared_ptr：实现共享式拥有概念；多个智能指针可以指向同一个对象，调用count()来计算共用的对象数，解决了auto_ptr上智能指向一个对象的问题
          成员函数：   
            use_count 返回引用计数的个数,赋值时，计数加1，指针过期时，计数减1，等最后一个指针过期时，才调用delete
            unique 返回是否是独占所有权( use_count 为 1)
            swap 交换两个 shared_ptr 对象(即交换所拥有的对象)
            reset 放弃内部对象的所有权或拥有对象的变更, 会引起原有对象的引用计数的减少
            get 返回内部对象(指针), 由于已经重载了()方法, 因此和直接使用对象是一样的.  
       4、weak_ptr ：是一种不控制对象生命周期的智能指针, 它指向一个 shared_ptr 管理的对象
          用来解决share_ptr指针相互指向带来的死锁问题
          weak_ptr 没有重载*和->但可以使用 lock 获得一个可用的 shared_ptr 对象. 注意, weak_ptr 在使用前需要检查合法性.  
          

​     expired 用于检测所管理的对象是否已经释放, 如果已经释放, 返回 true; 否则返回 false.  
​     lock 用于获取所管理的对象的强引用(shared_ptr). 如果 expired 为 true, 返回一个空的 shared_ptr; 否则返回一个 shared_ptr, 其内部对象指向与 weak_ptr 相同.  
​     use_count 返回与 shared_ptr 共享的对象的引用计数.  
​     reset 将 weak_ptr 置空.  
​     weak_ptr 支持拷贝或赋值, 但不会影响对应的 shared_ptr 内部对象的计数.  



​       
