# 函数指针与回调函数

## 指针函数与函数指针

`int* func(int a, int b)` 指针函数主体为函数，返回值为指针

`int (*func)(int a, int b)` 函数指针主体是指针，返回值为指定类型

~~~c++
// 函数指针
#include <iostream>

using namespace std;

int sum(int a, int b) {

	return a+b;

}

int main() {

	int a, b;
	cin >> a >> b;
	int (*func)(int a, int b);
	func = sum;
	cout << func(a, b); // 可以使用指向函数的功能
	return 0;

}
~~~

~~~c++
// 定义时需要注意
int *func(int a, int b); // *func定义时需加()
func = sum; // 此处报错

// 函数指针返回值类型与所指函数返回值类型不同时会报错
#include <iostream>

using namespace std;

doublesum(int a, int b) {

	return a+b;

}

int main() {

	int a, b;
	cin >> a >> b;
	int (*func)(int a, int b);
	func = sum; // 此处报错
	cout << func(a, b); // 可以使用指向函数的功能
	return 0;

}
~~~

## 回调函数

通过设计一个通用性较强的函数来满足使用者的需求

~~~c++
// 回调函数
#include <iostream>

using namespace std;

int compute_func(int (*func_ptr)(int, int), int x, int y) { // 开发者提供的通用性较强的函数

	return func_ptr(x, y);

}

int sum(int x, int y) { // 求和函数

	return x + y;

}

int difference(int x, int y) { // 求差函数
	
	return x - y;

}

int main() {

	cout << compute_func(sum, 1, 2) << endl;
	cout << compute_func(difference, 1, 2) << endl;
	return 0;

}
~~~



# 动态内存管理

## malloc， calloc，realloc，free

malloc与new的区别：

​		1.malloc只进行申请空间的操作，new在申请空间的同时也会调用构造函数。free与delete同理。

​		2.malloc/free是函数，new/delete是操作符。

​		3.malloc申请空间时需要手动计算空间大小并传递，new只需要在其后面跟上空间的类型即可。

​		4.malloc申请的空间不会初始化，new可以初始化。

​		5.malloc的返回值为void*，使用时需要强转；new返回的时指定类型。

​		6.malloc申请空间失败时返回的时NULL，因此必须判空。new不需要，但是new需要捕获异常。

malloc与calloc的区别

​		calloc在申请空间成功后，在返回地址之前把申请的空间，每个字节初始化为0。

## operator new与operator delete函数

语法：`(指针类型)operator new(sizeof(数据类型))`

语法与malloc相同，且同样不会调用构造函数与析构函数

### malloc与operator new的区别

malloc申请空间失败时会返回0。

operator new申请空间失败时会**抛异常**。

**抛异常**：面向对象的语言处理错误的方式。

operator new = malloc + 抛异常实现

new = operator new + 构造函数

**operator delete**与**free**的区别只有调用了析构函数，没有抛异常的实现。

## 内存池

​	重载new与delete使得申请空间不再向堆中申请，而是向内存池申请以提高申请速度。

~~~c++
void* operator new(size_t n) {
    
    void* p = nullptr;
    p = allocator<ListNode>().allocate(1);// 内存池
    cout << "memory pool allocate" << endl;
    return p;
    
}

void operator delete(void *p) {
    
    allocator<ListNode>().deallocate((ListNode*)p,1);
    cout << "memory pool deallocate" << endl;
    
}
~~~

## 定位new表达式（placement-new）

定位new表达式是在**已分配的原始内存空间中调用构造函数初始化一个对象**。(与operator new一起使用可以达到new的效果)

使用格式：

​	new(place_address)type或者new(place_address)type(initializer-list)

​	place_address必须是一个指针，initializer-list是类型的初始化列表

~~~c++
class A{
    
  ...  
    
};

int main() {
    
    
    return 
    
}
~~~

## 内存泄漏（c++独有问题）

### 什么是内存泄漏

​	内存泄漏指因为疏忽或错误造成程序未能释放已经不再使用内存的情况。内存泄漏并不是指内存在物理上的消失，而是应用程序分配某段内存后，因为设计错误，失去了对该段内存的控制，因而造成了内存的浪费。

### 内存泄漏的危害

​	长期运行的程序出现内存泄漏，影响很大，如操作系统、后台服务等等，出现内存泄漏会导致响应越来越慢，最终卡死。

### 如何在堆上申请4G的空间

​	将内存进程改为x64。

#  类和对象

## 对象特性

### 构造函数

### 初始化列表

初始化列表的初始化顺序是变量在类中声明的顺序。

#### 深拷贝与浅拷贝

##### 浅拷贝



~~~c++
#include <iostream>
#include <string>

using namespace std;

class Person {

public:

	Person() {
		cout << "Person默认构造函数的调用" << endl;
	}

	Person(int age, int height) {
		this->m_age = age;
		m_Height = new int(height);
		cout << "Person有参构造函数的调用" << endl;
	}

	~Person() {
		if (m_Height != NULL) {
			delete m_Height;
			m_Height = NULL;
		}
		cout << "Person析构函数的调用" << endl;
	}

	int m_age;
	int *m_Height;

};

void test01() {

	Person p1(18,160);
	cout << "p1的年龄为：" << p1.m_age << endl;
	Person p2(p1);
	cout << "p2的年龄为：" << p2.m_Height << endl;

}

int main() {

	test01();
	return 0;

}
~~~

##### 浅拷贝的问题

​	系统自动生成的拷贝构造函数会做**浅拷贝**操作，在析构函数释放内存空间时，会出现重复释放的情况。此时需要自己写一个拷贝构造函数来进行**深拷贝**操作。

##### 深拷贝

~~~c++
#include <iostream>
#include <string>

using namespace std;

class Person {

public:

	Person() {
		cout << "Person默认构造函数的调用" << endl;
	}

	Person(int age, int height) {
		this->m_age = age;
		m_Height = new int(height);
		cout << "Person有参构造函数的调用" << endl;
	}

	Person(const Person& p) {
		m_age = p.m_age;
		//m_Height = p.m_Height; 这是编译器默认实现的代码，此时施法空间便会出现重复释放的情况
		m_Height = new int(*p.m_Height); //此处实现深拷贝操作，防止内存重复释放
	}

	~Person() {
		if (m_Height != NULL) {
			delete m_Height;
			m_Height = NULL;
		}
		cout << "Person析构函数的调用" << endl;
	}

	int m_age;
	int *m_Height;

};

void test01() {

	Person p1(18,160);
	cout << "p1的年龄为：" << p1.m_age << endl;
	Person p2(p1);
	cout << "p2的年龄为：" << p2.m_Height << endl;

}

int main() {

	test01();
	return 0;

}
~~~

## 友元

### 全局函数做友元

​	**关键字：friend**

~~~c++
#include <iostream>
#include <string>

using namespace std;

class Building {

	// 通过关键字friend使得类外函数可以访问private权限内的内容
	friend void GoodGay(Building* building);

public:

	string m_SittingRoom;

private:

	string m_Bedroom;

};

void GoodGay(Building* building) {

	cout << "GoodGay函数正在访问" << building->m_SittingRoom << endl;
	
	cout << "GoodGay函数正在访问" << building->m_Bedroom << endl;

}

void test01() {

	Building building;
	GoodGay(&building);

}

int main() {

	test01();
	return 0;

}
~~~

### 友元类（类做友元）

~~~c++
#include <iostream>
#include <string>

using namespace std;

//类声明
class Building;

//类的实现
class GoodGay {

public:
	//构造函数 与 析构函数 实现在类外，需要在类内声明
	GoodGay();
	~GoodGay();

public:

	void visit();
	Building* building;

};

class Building {
	
	//通过 关键字friend 使的类获得访问 private 的权限
	friend class GoodGay;

public: //构造函数

	Building();

public:

	string m_SittingRoom; //客厅

private:

	string m_BedRoom; //卧室

};

//类外实现函数
Building::Building() {

	this->m_SittingRoom = "客厅";
	this->m_BedRoom = "卧室";

}

GoodGay::GoodGay() {

	//创建一个Building对象
	building = new Building;

}

GoodGay::~GoodGay() {

	delete building;
	building = NULL;

}

void GoodGay::visit() {

	cout << "GoodGay正在访问" << building->m_SittingRoom << endl;
	cout << "GoodGay正在访问" << building->m_BedRoom << endl;

}

int main() {

	GoodGay xiaoming;
	xiaoming.visit();
	return 0;

}
~~~

### 成员函数做友元

~~~c++
#include <iostream>
#include <string>

using namespace std;

// 类的声明
class Building;

// 类的实现
class GoodGay {

public:

	GoodGay();
	~GoodGay();
	void visit1();//使其可以访问Building的peivate权限
	void visit2();//使其不可访问Building的peivate权限
	Building* building;

};

class Building {

public:

	//通过 friend 关键字给予 GoodGay 类内函数 visit1 访问BedRoom的权限
	friend void GoodGay::visit1();
	Building();

	string m_SitingRoom;

private:

	string m_BedRoom;

};

// 类内函数的实现
Building::Building() {

	m_SitingRoom = "客厅";
	m_BedRoom = "卧室";

}

GoodGay::GoodGay() {

	building = new Building;

}

GoodGay::~GoodGay() {

	if (building != NULL) {
		delete building;
		building = NULL;
	}

}

void GoodGay::visit1() {

	cout << "GoodGay正在访问：" << building->m_SitingRoom << endl;
	cout << "GoodGay正在访问：" << building->m_BedRoom << endl;

}

void GoodGay::visit2() {

	cout << "GoodGay正在访问；" << building->m_SitingRoom << endl;
	//cout << "GoodGay正在访问；" << building->m_BedRoom << endl;  此时visit2没有访问的权限

}

int main() {

	GoodGay xiaoming;
	xiaoming.visit1();
	return 0;

}
~~~

## C++运算符重载

### 运算符重载概念

对已有的运算符重新进行定义，赋予其另一种功能，以适应不同的数据类型。

**运算符重载也可以发生函数重载**

### 加号运算符重载

​	作用：实现两个自定义数据类型相加时的运算。

​	实现：通过编译器提供的函数名**operator**来实现加号运算符的重载来达到想要的作用。

#### 使用成员函数模拟加法

~~~c++
#include <iostream>

using namespace std;

class Person {

public:

	int m_Age;
	int m_Height;
	Person PersonaddPerson(const Person& p) {
	
		Person temp;
		temp.m_Age = this->m_Age + p.m_Age;
		temp.m_Height = this->m_Height + p.m_Height;
		return temp;

	}

};

int main() {

	Person p1, p2, p3;
	p1.m_Age = 18;
	p1.m_Height = 50;
	p2.m_Age = 18;
	p2.m_Height = 50;
	p3 = p1.PersonaddPerson(p2);
	cout << p3.m_Age << endl << p3.m_Height << endl;
	return 0;

}
~~~

#### 通过成员函数重载+号

~~~c++
#include <iostream>

using namespace std;

class Person {

public:

	int m_Age;
	int m_Height;
	// 通过编译器提供的函数名 operator 来实现+号运算符的重载
	Person operator+(const Person& p) {

		Person temp;
		temp.m_Age = this->m_Age + p.m_Age;
		temp.m_Height = this->m_Height + p.m_Height;
		return temp;

	}

};

int main() {

	Person p1, p2, p3;
	p1.m_Age = 18;
	p2.m_Age = 18;
	p1.m_Height = 50;
	p2.m_Height = 50;
	p3 = p1 + p2;
	cout << p3.m_Age << endl << p3.m_Height << endl;
	return 0;

}
~~~

#### 通过全局函数重载加号

~~~c++
#include <iostream>

using namespace std;

class Person {

public:

	int m_Age;
	int m_Height;

};

Person operator+(const Person& p1, const Person& p2) {

	Person temp;
	temp.m_Age = p1.m_Age + p2.m_Age;
	temp.m_Height = p1.m_Height + p2.m_Height;
	return temp;

}

int main() {

	Person p1, p2, p3;
	p1.m_Age = p2.m_Age = 18;
	p1.m_Height = p2.m_Height = 50;
	p3 = p1 + p2;
	cout << p3.m_Age << endl << p3.m_Height << endl;
	return 0;

}
~~~

### 左移运算符重载

#### 作用

可以输出自定义的数据类型。

#### 使用成员函数模拟左移运算符

~~~c++
#include <iostream>

using namespace std;

class Person {

public:

	int m_Age;
	int m_Height;
	void output() {

		cout << m_Age << endl << m_Height << endl;

	}

};

int main() {

	Person p;
	p.m_Age = 18;
	p.m_Height = 50;
	p.output();
	return 0;

}
~~~

#### 通过全局函数重载左移运算符（无法通过成员函数重载）

~~~c++
#include <iostream>

using namespace std;

class Person {

public:

	int m_Age;
	int m_Height;
	// 无法在类内通过成员函数完成左移运算符 << 的重载， 因为其简化形式为 p << cout
	//void operator<< (ostream &cout) {
	//	cout << p.m_age << endl << p.m_height << endl;
	//}

};

ostream& operator<< (ostream& cout, Person& p) { // cout 属于标准输出流的对象，有且只能有一个

	cout << p.m_Age << endl << p.m_Height << endl;
	return cout;

}

//void operator<< (ostream& cout, Person& p) { // 此时使用cout << p << endl; 会报错，是因为返回值为空，当返回值为ostream时，为链式编程思想。
//
//	cout << p.m_Age << endl << p.m_Height << endl;
//
//}

int main() {

	Person p;
	p.m_Age = 18;
	p.m_Height = 50;
	cout << p << endl;
	return 0;

}
~~~

### 递增递减运算符的重载（自增自减运算符）

#### 作用

通过重载递增运算符，实现自己的整形数据。

#### 使用成员函数模拟重载递增运算符（后置递增无法实现）

~~~c++
#include <iostream>

using namespace std;

class MyInteger {

	//通过友元获取private权限
	friend ostream& operator<< (ostream& cout, const MyInteger& num);

public:

	MyInteger() {

		m_Num_1 = 10;
		m_Num_2 = 20;

	}
	//通过成员函数模拟递增运算符
	//前置
	void F_ProGressive() {

		++m_Num_1;
		++m_Num_2;

	}
	//后置
	/*MyInteger Q_ProGressive() {

		MyInteger temp;
		temp = *this;
		m_Num_1++;
		m_Num_2++;
		return temp;

	}*/
private:

	int m_Num_1;
	int m_Num_2;

};

//通过全局函数重载左移运算符
ostream& operator<< (ostream& cout, const MyInteger& num) {

	cout << num.m_Num_1 << endl << num.m_Num_2;
	return cout;

}

int main() {

	MyInteger num;
	cout << "递增前" << endl;
	cout << num << endl;
	num.F_ProGressive();
	cout << "前置递增后" << endl;
	cout << num << endl;
	num.Q_ProGressive();
	cout << "后置递增后第一次输出" << endl;
	cout << num << endl;
	cout << "后置递增后第二次输出" << endl;
	cout << num << endl;
	return 0;

}
~~~

#### 使用成员函数重载递增运算符（全局函数同理）

~~~c++
#include <iostream>

using namespace std;

class MyInteger {

	//通过友元获取private权限
	friend ostream& operator<< (ostream& cout, const MyInteger& num);

public:

	MyInteger() {

		m_Num_1 = 10;
		m_Num_2 = 20;

	}
	
	//前置
	MyInteger& operator++ () { //返回引用时因为链式，返回数值，重复进行运算时会出现数值错误的情况
		++m_Num_1;
		++m_Num_2;
		return *this;
	}
	//后置
	MyInteger operator++ (int) { //int为占位参数，便于函数重载，此处用于区分前置与后置

		MyInteger temp = *this;
		this->m_Num_1++;
		this->m_Num_2++;
		return temp;

	}

private:

	int m_Num_1;
	int m_Num_2;

};

//通过全局函数重载左移运算符
ostream& operator<< (ostream& cout, const MyInteger& num) {

	cout << num.m_Num_1 << endl << num.m_Num_2;
	return cout;

}

int main() {

	MyInteger num;
	cout << "前置递增" << endl;
	++num;
	cout << num << endl;
	cout << "后置递增" << endl;
	cout << num++ << endl;
	cout << num << endl;
	return 0;

}
~~~

#### 注意事项（前置返回引用，后置返回值）

#### 前置返回值时的BUG

~~~c++
// m_Num_1 = 10  m_Num_ = 20
MyInteger operator++ () { //返回引用时因为链式，返回数值，重复进行运算时会出现数值错误的情况
		++m_Num_1;
		++m_Num_2;
		return *this;
	}
//输出时
cout << ++(++num) << endl;
cout << num << endl;
//当返回引用时输出   12 22
//                12 22
//当返回值时输出 12 22
//			  11 21
~~~

### 赋值运算符重载

#### 为什么要做赋值运算符的重载

C++编译器最少会给一个类添加4个函数

​	1、默认构造函数（无参， 函数体为空）

​	2、默认析构函数（无参，函数体为空）

​	3、默认拷贝构造函数，对属性进行值拷贝

​	4、赋值运算符operator=，对属性进行值拷贝

如果类中有属性指向堆区，做赋值操作时也会出现深浅拷贝的问题。

#### 使用构造函数解决深浅拷贝问题（无法解决赋值时的深浅拷贝问题）

~~~c++
#include <iostream>

using namespace std;

class Person {

public:

	Person() {

	}

	Person(int age){
		m_Age = new int(age);
	}

	Person(const Person &p) {
		m_Age = new int(*p.m_Age);
	}
	~Person() {
		if (m_Age != NULL) {
			delete m_Age;
			m_Age = NULL;
		}
	}

	int *m_Age;

};

int main() {

	Person p1(18);
	Person p2(p1);
	//p2 = p1; 此处并未使用拷贝构造函数，所以仍有BUG
	cout << *p2.m_Age;
	return 0;

}
~~~

#### 使用成员函数重载赋值运算符解决深浅拷贝问题（编译器提示无法使用全局函数完成重载）

~~~c++
#include <iostream>

using namespace std;

class Person {

public:

	Person() {
		m_Age = new int(0);
	}

	Person(int age){
		m_Age = new int(age);
	}

	Person(const Person &p) {
		m_Age = new int(*p.m_Age);
	}
	~Person() {
		if (m_Age != NULL) {
			delete m_Age;
			m_Age = NULL;
		}
	}

	int *m_Age;

	Person& operator=(Person& p) {

		//系统实现的赋值操作为浅拷贝
		//m_Age = p.m_Age;

		//应先判断是否有属性在堆区，如果有应先释放干净，然后在进行深拷贝
		if (m_Age != NULL) {
			delete m_Age;
			m_Age = NULL;
		}

		//自行实现深拷贝
		m_Age = new int(*p.m_Age);
		return *this;

	}
};

int main() {

	Person p1(18), p2, p3;
	p2 = p1;
	cout << *p2.m_Age << endl;
	p3 = p2 = p1; //如果重载时无返回值或返回的不是自身，此时链式编程就会出现问题，结果错误或无法继续
	cout << *p3.m_Age << endl;
	return 0;

}
~~~

### 关系运算符重载

#### 作用

重载关系运算符，可以让两个自定义类型对象进行对比操作。

#### 通过成员函数重载运算符（全局函数重载同理）

~~~c++
#include <iostream>
#include <string>

using namespace std;

class Person {

public:

	string name;
	int age;

	bool operator==(const Person& p) {

		if ((this->name == p.name) && (this->age == p.age)) {
			return true; //相等返回true
		}
		else return false;//不相等返回false

	}

};

int main() {

	Person p1, p2;
	p1.name = p2.name = "TOM";
	p1.age = p2.age = 18;
	if (p1 == p2) cout << 1;
	else cout << 2;
	return 0;

}
~~~

### 函数调用运算符重载

**函数调用运算符()**也可以重载。

由于重载后使用的方式非常像函数的调用，因此称为仿函数。

仿函数没有固定写法，非常灵活。

#### 使用成员函数重载函数调用运算符（编译器无法使用全局函数重载）

~~~c++
#include <iostream>
#include <string>

using namespace std;

//创建输出类
class MyPrint {

public:

	void operator() (string s) { //第一个括号为要重载的运算符，第二个括号为参数列表
	
		cout << s << endl;
	
	}

};

//创建加法类
class MyAdd {

public:

	int operator() (int num1, int num2) {

		return num1 + num2;

	}

};

int main() {

	MyPrint s;
	s("123456"); //由于调用起来非常类似于函数调用，因此称为仿函数

	//匿名函数对象：不创建函数对象，直接输出
	cout << MyAdd()(100, 200) << endl;
	return 0;

}
~~~



### 总结

1.对于内置的数据类型的表达式的运算符是不可能改变的。只能改变自己定义的数据类型。

2.不要滥用运算符重载。例：在加号运算符重载中进行减法的运算。

## 继承

#### 作用

继承是面对对象三大特性之一。

定义类时，下级别的成员除了拥有上一级的共性，还有自己的特性。这时候问哦们就可以考虑利用继承技术，减少重复代码。

 ### 继承的基本语法

#### 基本语法

语法： class 子类：继承方式 父类

​	**子类**也称为**派生类**。

​	**父类**也称为**基类**。

#### 普通实现页面

~~~c++
#include <iostream>

using namespace std;

//JAVA页面
class JAVA {

public:
	void header() {
		cout << "首页、公开课、登录、注册...（公共头部）" << endl;
	}
	void footer() {
		cout << "帮助中心、交流合作、站内地图...（公共底部）" << endl;
	}
	void left() {
		cout << "Java、Python、C++...（公共分类列表）" << endl;
	}
	void content() {
		cout << "Java学科视频" << endl;
	}

};


//Python页面
class Python {

public:
	void header() {
		cout << "首页、公开课、登录、注册...（公共头部）" << endl;
	}
	void footer() {
		cout << "帮助中心、交流合作、站内地图...（公共底部）" << endl;
	}
	void left() {
		cout << "Java、Python、C++...（公共分类列表）" << endl;
	}
	void content() {
		cout << "Python学科视频" << endl;
	}

};

//C++页面
class CPP {

public:
	void header() {
		cout << "首页、公开课、登录、注册...（公共头部）" << endl;
	}
	void footer() {
		cout << "帮助中心、交流合作、站内地图...（公共底部）" << endl;
	}
	void left() {
		cout << "Java、Python、C++...（公共分类列表）" << endl;
	}
	void content() {
		cout << "C++学科视频" << endl;
	}

};

int main() {

	JAVA ja;
	Python py;
	CPP cpp;
	ja.content();
	ja.footer();
	ja.header();
	ja.left();
	cout << "---------------------" << endl;

	py.content();
	py.footer();
	py.header();
	py.left();
	cout << "---------------------" << endl;

	cpp.content();
	cpp.footer();
	cpp.header();
	cpp.left();
	cout << "---------------------" << endl;
	return 0;

}
~~~

#### 继承实现页面

~~~C++
#include <iostream>

using namespace std;

//公共页面类
class BasePage {

public:
	void header() {
		cout << "首页、公开课、登录、注册...（公共头部）" << endl;
	}
	void footer() {
		cout << "帮助中心、交流合作、站内地图...（公共底部）" << endl;
	}
	void left() {
		cout << "Java、Python、C++...（公共分类列表）" << endl;
	}

};

//JAVA页面
class JAVA: public BasePage{

public:
	
	void content() {
		cout << "Java学科视频" << endl;
	}

};


//Python页面
class Python: public BasePage {

public:
	
	void content() {
		cout << "Python学科视频" << endl;
	}

};

//C++页面
class CPP:public BasePage {

public:
	
	void content() {
		cout << "C++学科视频" << endl;
	}

};

int main() {

	JAVA ja;
	Python py;
	CPP cpp;
	ja.content();
	ja.footer();
	ja.header();
	ja.left();
	cout << "---------------------" << endl;

	py.content();
	py.footer();
	py.header();
	py.left();
	cout << "---------------------" << endl;

	cpp.content();
	cpp.footer();
	cpp.header();
	cpp.left();
	cout << "---------------------" << endl;
	return 0;

}
~~~

### 继承方式

#### 三种继承方式（公共继承、保护继承、私有继承）

**<u>子类无论在什么继承方式下都不可访问父类的私有权限</u>**

为**公共继承**时，public权限下的内容，在子类仍是public；protected权限下的内容，在子类仍是protected。

为**保护继承**时，父类中可访问的内容在子类中变为protected权限的内容。

为**私有继承**时，父类中可访问的内容在子类中变为private权限的内容。

#### 继承方式

~~~c++
 #include <iostream>

using namespace std;

//公共类 父类
class Base {

public:
	int m_A;
protected:
	int m_B;
private:
	int m_C;

};

//公共继承
class Son1 : public Base {

public:
	void func() {
		m_A = 10;	//父类中的公共成员，子类中仍为公共成员
		m_B = 10;	//父类中的保护成员，子类中仍为保护成员
		//m_C = 10;	//父类中私有成员，子类访问不到
	}

};

//保护继承
class Son2 : protected Base {

public:
	void func() {
		m_A = 10;	//父类中的公共成员，子类中变为保护成员
		m_B = 10;	//父类中的保护成员，子类中仍为保护成员
		//m_C = 10;	//父类中私有成员，子类访问不到
	}

};

//私有继承
class Son3 : private Base {

public:
	void func() {
		m_A = 10;	//父类中的公共成员，子类中变为私有成员
		m_B = 10;	//父类中的保护成员，子类中变为私有成员
		//m_C = 10;	//父类中私有成员，子类访问不到
	}

};

//测试
void test01() {

	Son1 s;
	s.m_A = 10;
	//s.m_B = 10;	父类中的保护成员，子类中仍为保护成员

}

void test02() {

	Son2 s;
	//s.m_A = 10;		父类中的公共成员，子类中变为保护成员
	//s.m_B = 10;	父类中的保护成员，子类中仍为保护成员

}

void test03() {

	Son3 s;
	//s.m_A = 10;		父类中的公共成员，子类中变为私有成员
	//s.m_B = 10;		父类中的保护成员，子类中变为私有成员

}

int main() {

	return 0;

}
~~~

### 继承中的对象模型

**问题**：从父类继承过来的成员，那些属于子类对象中？

#### 示例

~~~c++
#include <iostream>

using namespace std;

//公共类--父类--基类
class Base {

public:
	int m_A;
protected:
	int m_B;
private:
	int m_C; //私有成员只是被隐藏了，但是还是会继承下去

};

//子类--派生类
class Son: public Base {

public:
	int m_D;

};

int main() {
	
	cout << sizeof(Son); //输出结果		继承前为 4	继承后为 16
	return 0;

}
~~~

#### 利用开发人员命令提示符工具查看对象模型

1. 在系统开始界面所有应用中打开**开发人员命令提示符工具**
2. 跳转盘符 例： C:
3. 跳转到具体路径： cd 具体路径
4. 查看目录： dir
5. 输入 cl /d1 reportSingleClassLayout类名 文件名

#### 总结

- 父类中所有非静态成员都会被子类继承下去
- 父类中私有成员属性是被编译器给隐藏了因此访问不到，但是任然被子类继承

### 构造和析构顺序

先调用父类**构造**函数，再调用子类**构造**函数。

先调用子类**析构**函数，再调用父类**析构**函数。

![屏幕截图 2024-03-04 162956](C:\Users\sqrt\Pictures\Screenshots\屏幕截图 2024-03-04 162956.png)

### 继承同名成员处理方式

**问题**：当子类与父类出现同名的函数，如何通过子类对象，访问到子类或父类中同名的数据。

- 访问子类同名成员，直接访问即可。
- 访问父类同名成员，需要加作用域。

#### 示例

~~~c++
//当子类与父类存在同名成员时的处理方式
#include <iostream>

using namespace std;

class Base {

public:
	int m_A = 100;

};

class Son: public Base {

public:
	int m_A = 200;

};

int main() {

	Son s1;
	cout << s1.m_A << endl; //当直接调用成员时，调用的时子类的成员
	cout << s1.Base::m_A << endl; //当使用作用域调用时，会调用相应作用域的成员
	return 0;

}
~~~

#### 子类中的同名成员会隐藏父类中的同名成员，所以在无作用域时，父类的函数重载也无法识别。如果想访问父类中的成员函数，需要加作用域。

~~~c++
//当子类与父类存在同名成员时的处理方式
#include <iostream>

using namespace std;

class Base {

public:
	int m_A = 100;
	void func() {
		cout << "Base_func_1" << endl;
	}
	void func(int t) {
		cout << "Base_func_2" << endl;
	}

};

class Son: public Base {

public:
	int m_A = 200;
	void func() {
		cout << "Son_func" << endl;
	}

};

int main() {

	Son s1;
	cout << func(100); //此时此处会报错
	return 0;

}
~~~

### 继承同名静态成员处理方式

静态成员与非静态成员处理方式一致

~~~c++
#include <iostream>

using namespace std;

// 父类
class Base{

public:

    static void func() {
        cout << "Base 类静态成员函数调用输出测试" << endl;
    }

    static int m_A;

};
int Base::m_A = 100;

// 子类
class Son : public Base{

public:

    static void func() {
        cout << "Son 类静态成员函数调用输出测试" << endl;
    }

    static int m_A;

};
int Son::m_A = 200;

void test01() {
    cout << "成员变量输出测试" << endl << endl;
    cout << "第一种输出方式，通过对象访问" << endl;
    Son son_1;
    cout << "Son 类下成员变量输出" << son_1.m_A << endl;
    cout << "Base 类下成员变量输出" << son_1.Base::m_A << endl;
    cout << endl;
    cout << "第二种输出方式，通过类名输出" << endl;
    cout << "Son 类下成员变量输出" << Son::m_A << endl;
    cout << "Base 类下成员变量输出" << Son::Base::m_A << endl;

}

void test02() {

    cout << "成员函数输出测试" << endl << endl;
    cout << "第一种输出方式，通过对象访问" << endl;
    Son s1;
    s1.func();
    s1.Base::func();
    cout << endl;
    cout << "第二种输出方式，通过类名输出" << endl;
    Son::func();
    Son::Base::func();

}

int main() {

    //test01();
    test02();
    return 0;

}
~~~

### 多继承语法

语法： class 类名：继承方式  父类1， 继承方式  父类2

**父类种出现同名成员时，应该加作用域区分**

~~~C++
#include <iostream>

using namespace std;

class Base1{

public:
	Base1() {
		m_A = 100;
	}
	int m_A;

};

class Base2{

public:
	Base2() {
		m_B = 200;
	}
	int m_B;

};

class Son : public Base1, public Base2 {

public:
	Son() {
		m_C = 300;
	}
	int m_C;

};

void test01() {
	
	Son s1;
	cout << "多继承语法测试" << endl << endl;
	cout << "Base1类下成员函数m_A：" << s1.Base1::m_A << endl;
	cout << "Base2类下成员函数m_B：" << s1.Base2::m_B << endl;
	cout << "Son类下成员函数m_C：" << s1.m_C;

}

int main() {

	test01();
	return 0;

}
~~~

### 菱形继承

#### 什么是菱形继承

​	两个派生类继承同一个基类，又有某个类同时继承两个派生类，这种继承被称为**菱形继承**或**钻石继承**。、

#### 当菱形继承时，两个父类有相同的数据需要加作用域进行区分

~~~c++
#include <iostream>

using namespace std;

// 动物类
class Animal{

public:
	int m_Age;

};

// 羊类
class Sheep: public Animal{
};

// 驼类
class Tuo: public Animal{
};

// 羊驼类
class SheepTuo: public Sheep, public Tuo{
};

void test01() {

	// 当菱形继承时，两个父类有相同的数据需要加作用域进行区分
	SheepTuo sp1;
	sp1.Sheep::m_Age = 18;
	sp1.Tuo::m_Age = 20;
	cout << "sp1.Sheep::m_Age = " << sp1.Sheep::m_Age << endl;
	cout << "sp1.Tuo::m_Age = " << sp1.Tuo::m_Age << endl;

}

int main() {

	test01();
	return 0;

}
~~~

#### 虚继承（解决继承多余数据所造成的资源浪费问题）

~~~C++
#include <iostream>

using namespace std;

// 动物类
class Animal{

public:
	int m_Age;

};

// 羊类
class Sheep: virtual public Animal{
};

// 驼类
class Tuo: virtual public Animal{
};

// 羊驼类
class SheepTuo: public Sheep, public Tuo{
};

void test01() {

	SheepTuo sp1;
	sp1.Sheep::m_Age = 18;
	sp1.Tuo::m_Age = 20;
	cout << "sp1.Sheep::m_Age = " << sp1.Sheep::m_Age << endl;
	cout << "sp1.Tuo::m_Age = " << sp1.Tuo::m_Age << endl;
	// 当使用虚继承时，Animal类为虚基类，此时相同的数据相同的数据只保留一份，及只有一块空间
	cout << "sp1.m_Age = " << sp1.m_Age << endl;

}

int main() {

	test01();
	return 0;

}
~~~

## 多态

### **多态的基本概念**

​	多态分两类：

​		静态多态：**函数重载**和**运算符重载**属于静态多态，复用函数名

​		动态多态：派生类和虚函数实现运行时多态

​	静态多态和动态多态的区别：

​		静态多态函数地址早绑定 - 编译阶段确定函数地址

​		动态多态函数地址晚绑定 - 运行阶段确定函数地址

#### 编译时地址早绑定

~~~c++
#include <iostream>

using namespace std;

class Animal{

public:
    void dospeak() {
        cout << "Animal" << endl;
    }

};

class Cat: public Animal {

public:
    void dospeak() {
        cout << "Cat" << endl;
    }

};

// 引用时转换为父类，在编译时内存早编译
void Dospeak(Animal &animal) {
    
    animal.dospeak();

}

void test01() {

    Cat cat;
    Dospeak(cat);
    // 输出结果为 animal

}

int main() {

    test01();
    return 0;

}
~~~

#### 编译时内存晚绑定

~~~c++
#include <iostream>

using namespace std;

class Animal{

public:
    virtual void dospeak() { // 虚函数使编译时晚绑定
        cout << "Animal" << endl;
    }

};

class Cat: public Animal {

public:
    void dospeak() {
        cout << "Cat" << endl;
    }

};

void Dospeak(Animal &animal) {

    animal.dospeak();
    // 输出 Cat

}

void test01() {

    Cat cat;
    Dospeak(cat);

}

int main() {

    test01();
    return 0;

}
~~~

### **动态多态满足条件**

1. 有继承关系
2. 子类重写父类虚函数

动态多态使用：父类的指针或者引用 执行子类对象

**重写**：函数返回值类型 函数名 参数列表 完全一致称为重写。

### **多态原理**

 	父类使用virtual修饰后会产生vfptr（虚函数（表）指针）指向vftable（虚函数表）。当子类继承父类时会继承父类的vfptr，vfptr指向子类的vftable，当子类重写父类的虚函数时，子类的vftable会重写，而父类不变。

​	当父类的指针或者引用指向子类对象时，发生多态。

### **多态的优点**

- 代码组织结构清晰
- 可读性强
- 利于前期和后期的扩展以及维护

#### 	开闭原则

​		对扩展进行开放，对修改进行关闭。

### **纯虚函数和抽象类**

	#### 	纯虚函数

​		在多态中，通常父类中虚函数的实现是毫无意义的，主要都是调用子类重写的内容。因此可以将虚函数改为**纯虚函数**。

​	语法：`virtual 返回值类型 函数名 （参数列表） = 0`

	#### 	**抽象类**

​		当类中有了纯虚函数，这个类也称为**抽象类**。

		##### 		抽象类特点

- 无法实例化对象
- 子类必须重写抽象类中的纯虚函数，否则也舒适与抽象类。	

~~~c++
// 纯虚函数与抽象类
#include <iostream>

using namespace std;

class Base{

public:
	virtual void func() = 0;

};

class Son: public Base{

public:



};

// 纯虚函数无法实例化
void test01() {

	// Base b;
	// new Base;
	// 此处发生错误，报错信息为纯虚函数无法实例化

}

// 抽象类的子类不重写纯虚函数时，子类也为抽象类
void test02() {

	//Son s;
	// 此处报错

}

int main() {


	return 0;

}
~~~

### **虚析构和纯虚析构**

​	多态使用时，如果子类中有属性开辟到堆区，那么父类指针在释放时无法调用到子类的析构代码。

​	解决方法：将父类中的析构函数改为虚析构或纯虚析构

#### 	虚析构和纯虚析构的共性

- 可以解决父类指针释放子类对象
- 都需要有具体的函数实现

	#### 	虚析构和纯虚析区别

- 如果时纯虚析构，该类属于抽象类，无法实例化对象

​	虚析构语法：`virtual ~类名(){}`

​	纯虚析构语法：类内`virtual ~类名() = 0`

​				     类外`类名::类名(){}`

有了纯虚析构类也属于抽象类。

#### 总结

1. 虚析构或纯虚析构就是用来解决父类指针释放子类对象
2. 如果子类中没有堆区数据可以不写虚析构或纯虚析构
3. 拥有纯虚析构的类也属于抽象类

# 模板（泛型编程）

## 模板的概念

模板就是建立通用的模具，大大提高复用性。

## 模板的原理

编译器根据调用函数模板和类模板的类型，实例化出对应的函数。

## 函数模板

### 作用

建立一个通用函数，其函数返回值类型和形参类型可以不具体制定，用一个**虚拟的类型**来代表。

### 函数模板基本语法

~~~c++
template <typename T>
void func(T A, T B) {} //函数声明和定义
//template --- 声明创建模板
//typename --- 表明其后面的符号时一种数据类型，可以用class代替
//template <class T>
//T --- 通用的数据类型，名称可以替换，通常为大写字母
~~~

### 利用模板实现交换函数

~~~c++
#include <iostream>

using namespace std;

template <typename T>

void MySwap(T& x, T& y) {

	T z;
	z = x;
	x = y;
	y = z;

}



int main() {

	int a = 3, b = 4;
	cout << "交换前" << endl;
	cout << a << ' ' << b << endl;
	//模板的两种使用方式
	//自动类型推导：让编译器自动识别数据类型
	cout << "第一次交换后" << endl;
	MySwap(a, b);
	cout << a << ' ' << b << endl;
	//显示指定类型：告诉编译器要进行处理的类型
	cout << "第二次交换后" << endl;
	MySwap<int>(a, b);
	cout << a << ' ' << b << endl;
	return 0;

}
~~~

 ### 函数模板注意事项

#### 自动类型推导，必须推到出一致的数据类型T，才可以使用。

在同一个作用域下，模板的数据类型必须一致。

~~~C++
void MySwap(T& x, T& y) {

	T z;
	z = x;
	x = y;
	y = z;

}
// 传入的下x， y必须数据类型一致，不能一个int，一个double，数据类型不同
~~~

#### 模板必须要确定出T的数据类型，才可以使用。

~~~c++
#include <iostream>

using namespace std;

template <class T>
void funs() {
	cout << 1 << endl;
}

int main() {
    
    //funs(); 此时是错误的，因为无法推导出T的数据类型
    funs<int>(); //当指出正确的数据类型时，才可以正确运行
    return 0;
    
}
~~~

### 函数模板案例---数组排序

选择排序法

~~~c++
#include <iostream>
#include <string>

using namespace std;

//交换函数模板
template <class T>
void MySwap(T& A, T& B) {

	T C;
	C = A;
	A = B;
	B = C;

}

//排序算法模板 选择排序法
template <typename T>
void MySort_D(T arr[], int len) {

	T max;
	for (int i = 0; i < len; i++) {
		max = i;
		for (int j = i + 1; j < len; j++) {
			if (arr[max] < arr[j]) max = j;
		}
		if (max != i) MySwap(arr[max], arr[i]);
	}

}

//打印函数模板
template <typename T>
void MyPrint(T arr[], int len) {

	for (int i = 0; i < len; i++) {
		cout << arr[i] << '\t';
	}

}

int main() {

	char c[] = "abcdef";
	MySort_D(c, sizeof(c) / sizeof(char));
	MyPrint(c, sizeof(c) / sizeof(char));
	return 0;

}
~~~

### 普通函数与函数模板的区别

- 普通函数调用时可以发生自动类型转换（隐式类型转换）
- 函数模板调用时，如果利用自动类型推导，不会发生隐式类型转换
- 如果利用显示指定类型的方式，可以发生隐式类型转换

#### 示例

普通函数

~~~C++
#include <iostream>

using namespace std;

//普通函数
int MyAdd(int x, int y) {

	return x + y;

}

int main() {

	int a = 10;
	char b = 'c';
	cout << MyAdd(a, b) << endl; //a为整型，b为字符型。在传递参数时会进行隐式类型转换。
	return 0;

}
~~~

模板（未指定类型）（自动类型推到）

~~~c++
#include <iostream>

using namespace std;

//函数模板
template <typename T>
T MyAdd(T x, T y) {

	return x + y;

}

int main() {

	int a = 10;
	char b = 'c';
	cout << MyAdd(a, b) << endl; //a为整型，b为字符。由于未指定数据类型，传递参数时不会进行隐式类型转换
	return 0;

}
~~~

模板（指定类型）

~~~c++、
#include <iostream>

using namespace std;

//函数模板
template <typename T>
T MyAdd(T x, T y) {

	return x + y;

}

int main() {

	int a = 10;
	char b = 'c';
	cout << MyAdd<int>(a, b) << endl; //a为整型，b为字符。当指定数据类型时，在传递参数时会进行隐式类型转换。
	return 0;

}
~~~

### 普通函数与函数模板调用规则

调用规则如下：

1. 如果函数模板与普通函数都可以实现，优先调用普通函数。
2. 可以通过空模板参数列表来强制调用函数模板。
3. 函数模板也可以发生重载。
4. 如果函数模板可以产生更好的匹配，优先调用函数模板。

#### 示例

1.如果函数模板与普通函数都可以实现，优先调用普通函数。

~~~c++
#include <iostream>

using namespace std;

void MyPrint(int x, int y) {

	cout << "调用普通函数" << endl;

}

template <typename T>
void MyPrint(T x, T y) {

	cout << "调用模板函数" << endl;

}

int main() {

	MyPrint(1, 2);//调用普通函数
	MyPrint(0.2, 0.3);//调用模板函数
	return 0;

}
~~~

2.通过空模板参数列表来强制调用函数模板。

~~~c++
#include <iostream>

using namespace std;

void MyPrint(int x, int y) {

	cout << "调用普通函数" << endl;

}

template <typename T>
void MyPrint(T x, T y) {

	cout << "调用模板函数" << endl;

}

int main() {

	MyPrint<>(1, 2); //通过空模板参数列表来强制调用函数模板
	return 0;

}
~~~

3.函数模板重载

~~~c++
//通过参数个数的不同完成函数的重载
#include <iostream>

using namespace std;

template <class T>
void MyPrint(T a) {
	cout << "调用函数模板1" << endl;
}
template <class T>
void MyPrint(T a, T b) {
	cout << "调用函数模板2" << endl;
}

int main() {

	MyPrint(1);
	MyPrint(1, 2);
	return 0;

}
~~~

4.如果函数模板可以产生更好的匹配，优先调用函数模板。

~~~c++
#include <iostream>

using namespace std;

viod MyPrint(int x, int y) {
    cout << "调用普通函数" << endl;
}

template <typename T>
void MyPrint(T x, T y) {
    cout << "调用函数模板" << endl;
}

int main() {
	
    MyPrint('v', 't');//此时会调用函数模板
	return 0;
    
}
~~~

#### 总结

使用函数模板时，尽量不要使用普通函数，以免出现二义性。

## 模板的局限性

局限性：模板的通用性并不是万能的。

# STL

## String类

