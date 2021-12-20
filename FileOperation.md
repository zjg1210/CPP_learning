文件内容解析方法：

```
//文件没有数据的情况
	char ch;
	ifs >> ch;
	if (ifs.eof())//true为空
	{
		//cout << "文件为空！" << endl;
		this->Isempty = true;
		ifs.close();
		return;
	}	
	this->Isempty = false;
	ifs.putback(ch);//将数据放回去
	string buf;//用字符串的形式
    int index=0;//第几届

while (getline(ifs, buf)) //ifs是文件流，buf是存入的字符串
{
	vector<string>V;//存放6个string数据
	//10009,84.2375,10001,81.8,10006,80.925,  将文件的数据解析出来，即去掉“，”
	int pos=-1;//来记录‘，’的位置
	int start = 0;
	
	while(true)
	{ 
	pos = buf.find(',',start);//寻找','的位置
	if (pos == -1)
	{
		break;//未找到
	}
	string temp = buf.substr(start,pos-start);
	//cout << temp << endl;//放到另一个容器中
	V.push_back(temp);
	start = pos + 1;//下一个数据
    //cout << buf << endl;
	}
	this->mvp.insert(make_pair(index,V));//不是直接放temp,mvp类型是int,vector<string>
    index++;
}  
ifs.close();
```

文件内容清除的方法：
一、（内容是由容器来传入的）

> ​      fstream ofs;
> ​      ofs.open("speechContest.csv",ios::out);//文件存在就删除并重建,清空不用trunc,也不知为什么会不能清空用trunc
> ​	    ofs.close();                            //用out相当于重新建立一个，覆盖以前的

二、（文件内容是由数组传入的）
/这种方法还不是很好，应该要将每个成员一个个释放，但这种方法代码简洁

> ​			delete[]this->m_eptArray;
> ​			this->FileisEmpty = true;
> ​			cout << "清空成功！" << endl;
> ​			this->m_eptArray = NULL; //数组初始化
> ​			this->m_Num = 0; //还要对员工人数进行清零
> ​			this->Save(); //保存数据在文件中 ,从而实现真正的清空文件

三、关于文件的操作
1、文件的读取
  方法一：对于文件的读取有很多种，常见的是平时训练出现的，详细看以前的代码。

```
   ifstream ifs;
   ifs.open();
   if(ifs.is_open())
   {

   }
   //读数据
	/**** 方式一 ****/
	/*
	char buf[1024] = {0};
	while (ifs >> buf)
	{
		cout << buf << endl;
	}
	*/
	//方式二
	/*
	char buf[1024] = { 0 };
	while (ifs.getline(buf,sizeof(buf))) //getline()用来获取行，buf是用来存放的，需要里面的空间大小
	{
		cout << buf << endl;
	}
	*/
	//方式三
	string buf;//用字符串的形式
	while (getline(ifs, buf)) //ifs是文件流，buf是存入的字符串
	{
		cout << buf << endl;
	}
```

方法二：还有另一种特别方便 采用到sstream 中的stringstream

>   assert(ifs.is_open()); //确认打开文件
>   assert(getline(ifs,line));//读取文件中的一行内容放在line中
>   stringstream ss(line);//将line中的内容放在ss中
>   int a,b;
>   ss>>a>>b;//将ss中的内容解释出分别放在a和b中。

