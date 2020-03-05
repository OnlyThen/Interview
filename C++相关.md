# 类、继承、访问控制
```C++
class A {
	private: //只有类和友元函数可以访问私有成员
		int count__;
		static int objectCount; //类静态成员
		
	protected: //保护成员在派生类（即子类）中是可访问的
		int num__;
		
	public:
		std::string key__;
		A(int count, int num, std::string key);
		A(const A& obj);//拷贝构造函数是使用同一类中之前创建的对象来初始化新创建的对象，如果在类中没有定义拷贝构造函数，编译器会自行定义一个。
		~A();
		void change(int count); //成员函数
		friend void printCount(A a); //友元函数
		static int getCount();
};
//有的类有一个数据成员是指针，或者是有成员表示在构造函数中分配的其他资源，这两种情况下都必须定义拷贝构造函数。
A::A(int count, int num, std::string key): count__(count), num__(num) {
	key__ = key;
	objectCount += 1;
}

A::A(const A& obj) {
	count__ = obj.count__;
	num__ = obj.num__;
	key__ = obj.key__;
}
void A::change(int count) {
	this->count__ = count;
}
void printCount(A a) {
	std::cout << a.count__ << std::endl;
}

inline int Max(int x, int y) { //内联函数
   return (x > y)? x : y;
}

static int getCount(){
	return objectCount;
}
```
## 友元函数`friend`
> 定义在类外部，但有权访问类的所有私有（private）成员和保护（protected）成员。尽管友元函数的原型有在类的定义中出现过，但是友元函数并不是成员函数。

因为友元函数没有this指针，则参数要有三种情况：
* 要访问非static成员时，需要对象做参数；
* 要访问static成员或全局变量时，则不需要对象做参数；
* 如果做参数的对象是全局对象，则不需要对象做参数.
## 内联函数`inline`
> 如果一个函数是内联的，那么在编译时，编译器会把该函数的代码副本放置在每个调用该函数的地方。在类定义中的定义的函数都是内联函数，即使没有使用 inline 说明符。

Tip:
* 在内联函数内不允许使用循环语句和开关语句；
* 内联函数的定义必须出现在内联函数第一次调用之前；
* 时间交换空间。

## this指针
> 每一个对象都能通过`this`指针来访问自己的地址。this 指针是所有成员函数的隐含参数。

Tip:
* 当我们调用成员函数时，实际上是替某个对象调用它。
* this指向的地址不能修改。

## 继承
> 继承机制是面向对象程序设计是代码复用的重要手段，它允许程序员在保持类原有特性基础下，进行扩展增加功能。这样产生新的类，称为派生类，继承体现了面向对象设计的层次结构，体现了由简单到复杂的认知过程。

类成员/继承方式 | public继承 | protected继承 | private继承
------------ | ---------- | ------------- | -----------
基类public成员 | 派生类的public成员 | 派生类的protected成员 | 派生类的private成员
基类protected成员 | 派生类的protected成员 | 派生类的protected成员 | 派生类的private成员
基类private成员 | 不可见，通过基类接口访问 | 不可见，通过基类接口访问 | 不可见，通过基类接口访问

总结 

1. 基类的私有成员在派生类中无法访问，如果类内成员不想在类外访问但是想在派生类中访问，可以把类内成员定义为保护，可以看出保护成员限定符是因为继承才产生的。
2. public继承是一个接口继承，他遵从`is-a`的原则，每个父类的可用成员对子类也可用，因为每个子类对象也都是一个父类对象。 
3. protect/private继承是一个现实继承，基类的部分成员并非完全成为子类接口的一部分，是`has-a`原则，所以大多数情况下不会使用这两种继承关系，在绝大多数情况下使用public继承。 
4. 不管是哪种继承方式，基类的公有和保护成员派生类都可以访问，基类的私有成员存在，但是在派生类中不能访问。 
5. 当不写继承的关键字时，classs默认为私有继承，struct默认为公有继承。

```C++
class B: public A {
public:
	int getNum() {
		return num__;
	}
};
```

# 重载、多态
## 重载
### 函数重载
> 在同一个作用域内，可以声明几个功能类似的同名函数，但是这些同名函数的形式参数（指参数的个数、类型或者顺序）必须不同。

```
class printData
{
   public:
      void print(int i) {
        cout << "整数为: " << i << endl;
      }
 
      void print(double  f) {
        cout << "浮点数为: " << f << endl;
      }
 
      void print(char c[]) {
        cout << "字符串为: " << c << endl;
      }
};
```
### 运算符重载
> 重载的运算符是带有特殊名称的函数，函数名是由关键字 operator 和其后要重载的运算符符号构成的。与其他函数一样，重载运算符有一个返回类型和一个参数列表。

```
// 重载 + 运算符，用于把两个 Box 对象相加
      Box operator+(const Box& b)
      {
         Box box;
         box.length = this->length + b.length;
         box.breadth = this->breadth + b.breadth;
         box.height = this->height + b.height;
         return box;
      }
```
## 多态
> 当类之间存在层次结构，并且类之间是通过继承关联时，就会用到多态。多态意味着调用成员函数时，会根据调用函数的对象的类型来执行不同的函数。通过虚函数实现。

# 虚函数、虚函数表、纯虚函数
##虚函数（重写）
> 虚函数 是在基类中使用关键字`virtual`声明的函数。在派生类中重新定义基类中定义的虚函数时，会告诉编译器不要静态链接到该函数。我们想要的是在程序中任意点可以根据所调用的对象类型来选择调用的函数，这种操作被称为动态链接，或后期绑定。

```C++
class Shape {
   protected:
      int width, height;
   public:
      Shape( int a=0, int b=0)
      {
         width = a;
         height = b;
      }
      virtual int area()
      {
         cout << "Parent class area :" <<endl;
         return 0;
      }
};
```

## 纯虚函数
> 在基类中定义虚函数，以便在派生类中重新定义该函数更好地适用于对象，但是在基类中又不能对虚函数给出有意义的实现，这个时候就会用到纯虚函数。

```
class Shape {
   protected:
      int width, height;
   public:
      Shape( int a=0, int b=0) {
         width = a;
         height = b;
      }
      // pure virtual function
      virtual int area() = 0;
};
```

## 虚函数表
程序的堆区中有一个虚函数表指针，这个指针指向一张虚函数表，表中的数据为函数指针。
* 构造函数不能是虚函数，因为构造函数调用之前对象未初始化，这样虚函数表也未初始化，这样相矛盾。
* 析构函数可以是虚函数且推荐最好设置为虚函数，我们希望可以调用派生类的析构函数对新定义的成员也进行析构。

# 数据抽象
## pimpl惯用法
> 将类头文件中的需要隐藏变量定义到一个内部类中。

* 保护核心数据和实现原理；
* 降低编译依赖，提高编译速度；
* 接口与实现分离

# 接口（抽象类）
> C++ 接口是使用抽象类来实现的，如果类中至少有一个函数被声明为纯虚函数，则这个类就是抽象类。

设计抽象类（通常称为 ABC）的目的，是为了给其他类提供一个可以继承的适当的基类。抽象类不能被用于实例化对象，它只能作为接口使用。如果试图实例化一个抽象类的对象，会导致编译错误。

# Lambda表达式
> C++ 11 中的 Lambda 表达式用于定义并创建匿名的函数对象，以简化编程工作。

`[capture](parameters) mutable -> return-type {statement}`

* `[capture]`：捕捉列表。捕捉列表总是出现在lambda表达式的开始处。事实上，`[]`是lambda引出符。编译器根据该引出符判断接下来的代码是否是lambda函数。捕捉列表能够捕捉上下文中的变量供lambda函数使用。
* `(parameters)`：参数列表。与普通函数的参数列表一致。如果不需要参数传递，则可以连同括号`()`一起省略。
* `mutable`：修饰符。默认情况下，lambda函数总是一个const函数，`mutable`可以取消其常量性。在使用该修饰符时，参数列表不可省略（即使参数为空）。
* `-> return_type`：返回类型。用追踪返回类型形式声明函数的返回类型。出于方便，不需要返回值的时候也可以连同符号`->`一起省略。此外，在返回类型明确的情况下，也可以省略该部分，让编译器对返回类型进行推导。
* `{statement}`：函数体。内容与普通函数一样，不过除了可以使用参数之外，还可以使用所有捕获的变量。在lambda函数的定义式中，参数列表和返回类型都是可选部分，而捕捉列表和函数体都可能为空，`C++`中最简单的lambda函数只需要声明为：`[]{}`;

C++变量传递有传值和传引用的区别。可以通过前面的[]来指定：
* `[]` 沒有定义任何变量。使用未定义变量会引发错误；
* `[x, &y]` x以传值方式传入（默认），y以引用方式传入

# 内存分配、管理
## malloc、free、calloc、realloc、new、delete
`void* malloc (size_t size);`，当分配失败时，返回`NULL`指针。

`void free (void* ptr);`用于释放之前分配内存的所有权。如果`ptr`是未指向被分配的内存块，那么将造成未知行为。此操作不会修改`ptr`。

在操作系统角度来看，分配内存有两种方式，一种是采用推进brk指针来增加堆的有效区域来申请内存空间，还有一种是采用mmap是在进程的虚拟地址空间中（堆和栈中间，称为文件映射区域的地方）找一块空闲的虚拟内存。这两种方式都是分配虚拟内存，只有当第一次访问虚拟地址空间时，操作系统给分配物理内存空间。 
malloc是采用brk的方式来动态分配内存。

```
char* p = (char*)malloc(1); //从堆里面获得空间
if(p == NULL) {
	exit(1);
}
*p = 'y';
int a = sizeof(*p);
std::cout << &p << std::endl; //0x7ffeefbff468
std::cout << a << std::endl; //1
free(p);
a = sizeof(*p);
std::cout << a << std::endl; //1
std::cout << &p << std::endl; //0x7ffeefbff468
p == NULL;
```

`void* calloc (size_t num, size_t size);`用于分配`num`个大小为`size`的内存块，且初始化为全0。

`void* realloc (void* ptr, size_t size);`重新分配一块内存，可能原址修改，也可能拷贝到其他地址上。

```
void* operator new (std::size_t size); //throwing
void* operator new (std::size_t size, const std::nothrow_t& nothrow_value) noexcept; //nothrow
void* operator new (std::size_t size, void* ptr) noexcept; //placement
```
new

1. 首先会调用一个名为operator new或operator new[]的函数分配一块足够大的，原始的(没有清零)内存空间以便存储特定类型的对象;
2. 然后运行相应的构造函数以构造这些对象;
3. 最后返回指向该对象的指针。

delete

1. 对所指的对象或所指的数组中的元素指向对应的析构函数;
2. 然后调用operator delete或operator delete[]释放内存空间。

```
try {
	int* myarray= new int[10000];
} catch(std::bad_alloc& ba) {
	std::cerr << "bad_alloc caught: " << ba.what() << '\n';
}
```
## const
> 修饰不可改变的量

* const常量在全局作用域声明时必须初始化，否则无法通过编译。
* const常量在普通函数中一样，也必须在声明时就进行初始化。
* 在类定义体中，不初始化也能通过声明。但不能在类定义体中进行初始化，只能在构造时初始化。
* C++规定，只有“const static的整形成员”才可以在类定义体中进行初始化。

## static
* 静态局部变量：用于函数体内部修饰变量，这种变量的生存期长于该函数。
* 静态全局变量：定义在函数体外，用于修饰全局变量，表示该变量只在本文件可见。
* 静态函数：准确的说，静态函数跟静态全局变量的作用类似。
* 静态数据成员：用于修饰类的数据成员，即所谓“静态成员”。这种数据成员的生存期大于类的对象（实体 instance）。静态数据成员是每个类有一份，普通数据成员是每个实例有一份，因此静态数据成员也叫做类变量，而普通数据成员也叫做实例变量。不能在类定义中初始化，可以在类的外部通过使用范围解析运算符 :: 来重新声明静态变量从而对它进行初始化。
* 静态成员函数：用于修饰类的成员函数。静态成员函数只能访问静态成员数据、其他静态成员函数和类外部的其他函数，不具有this指针。

[参考内容](https://www.polarxiong.com/archives/C-C-中已初始化-未初始化全局-静态-局部变量-常量在内存中的位置.html)

## 智能指针
> 智能指针提供自动的、异常-安全的对象生命周期内存管理。`<memory>`

### ~~auto_ptr~~ 
`deprecated in C++11,removed in C++17`

### unique_ptr 
`C++ 11`
它持有对对象的独有权——两个unique_ptr不能指向一个对象，即unique_ptr不共享它所管理的对象。它无法复制到其他 unique_ptr，无法通过值传递到函数，也无法用于需要副本的任何标准模板库算法。只能移动 unique_ptr，即对资源管理权限可以实现转移。这意味着，内存资源所有权可以转移到另一个 unique_ptr，并且原 unique_ptr 不再拥有此资源。实际使用中，建议将对象限制为由一个所有者所有，因为多个所有权会使程序逻辑变得复杂。

### shared_ptr 
`C++ 11`
shared_ptr 是一个标准的共享所有权的智能指针，允许多个指针指向同一个对象，定义在 memory 文件中，命名空间为 std。shared_ptr最初实现于Boost库中，后由 C++11 引入到 C++ STL。shared_ptr 利用引用计数的方式实现了对所管理的对象的所有权的分享，即允许多个 shared_ptr 共同管理同一个对象。

### weak_ptr 
`C++ 11`
weak_ptr 被设计为与 shared_ptr 共同工作，可以从一个 shared_ptr 或者另一个 weak_ptr 对象构造而来。weak_ptr 是为了配合 shared_ptr 而引入的一种智能指针，它更像是 shared_ptr 的一个助手而不是智能指针，因为它不具有普通指针的行为，没有重载 operator* 和 operator-> ，因此取名为 weak，表明其是功能较弱的智能指针。它的最大作用在于协助 shared_ptr 工作，可获得资源的观测权，像旁观者那样观测资源的使用情况。观察者意味着 weak_ptr 只对 shared_ptr 进行引用，而不改变其引用计数，当被观察的 shared_ptr 失效后，相应的 weak_ptr 也相应失效。weak_ptr 可用于打破循环引用。引用计数是一种便利的内存管理机制，但它有一个很大的缺点，那就是不能管理循环引用的对象。



# 多线程
# 面向对象思想、特性
## 面向对象的 S.O.L.I.D 原则
1. S - 职责单一原则 Single Responsibility Principle (SRP)
	> 一个类，只对一件事负责，并把这件事做好，其只有一个能引起它变化的原因。
	
2. O - 开闭原则 Open-Closed Principle (OCP)
	> 模块是可扩展的，但不可被修改。
	
3. L - 里氏代换原则 Liskov Substitution Principle (LSP)
	> 子类必须能够替换成它们的基类。
	
4. I - 接口隔离原则 Interface Segregation Principle (LSP)
	> 实现专用接口
	
5. D - 依赖倒置原则 Dependency Inversion Principle (DIP)
	> 高层模块不应该依赖于低层模块的实现，而是要依赖于抽象。
	


# I/O
# 模板类
# 异常
# 网络异步操作
## select, epoll
# std
## 迭代器

## map
> map是STL的一个关联容器，它以<key,value>一对一的形式存储，且map的内部自建一个红黑树，使得其可以自动排序。

插入
```C++
map<int, int> Exm;
Exm.insert(pair<int, int>(0, 1));
Exm.insert(map<int, int>::value_type(2, 3)); //相同key不会覆盖
Exm[4] = 5; //相同key会覆盖
```
遍历
```C++
map<int, int>::iterator iter;      //迭代器
map<int, int>::reverse_iterator r_iter;  //反向迭代器
for(iter = Exm.begin(); iter != Exm.end(); iter += 1) {//正向遍历
	cout<<iter->first<<"   "<<iter->second<<endl;
}
for(r_iter = Exm.rbegin(); r_iter != Exm.rend() ; r_iter++ ) {
	cout<<r_iter->first<<"   "<<r_iter->second<<endl;
}
//map的基本操作函数
begin()   //返回指向头部的迭代器
clear()    //删除所有元素
count()   //返回指定元素出现的次数
end()      //返回指向尾部的迭代器
insert()   //插入元素
```
### unordered_map
> 该容器的底层是由哈希函数组织实现的。元素的顺序并不是由键值决定的，而是由键值的哈希值确定的。

### 哈希表工作原理
1. 先根据给定的key和散列算法得到具体的散列值，也就是对应的数组下标。
2. 根据数组下标得到此下标里存储的指针，若指针为空，则不存在这样的键值对，否则根据此指针得到此链式数组。（此链式数组里存放的均为一对对<Key,Value>）。
3. 遍历此链式数组，分别取出Key与给定的Key比较，若找到与给定key相等的Key，即在此hash表中存在此要查找的<Key,Value>键值对，此后便可以对此键值对进行相关操作；若找不到，即为不存在此 键值对。
4. 
## vector

### 倍增扩容
> Vector通过一个连续的数组存放元素，如果集合已满，在新增数据的时候，就要分配一块更大的内存，将原来的数据复制过来，释放之前的内存，在插入新增的元素，VS2015中以1.5倍扩容，GCC以2倍扩容。
参考此篇[文章](http://www.drdobbs.com/c-made-easier-how-vectors-grow/184401375)

两倍或更多：使内存可重用。

## list
> list是线性双向链表结构，它的数据由若干个节点构成，每一个节点都包括一个信息块（即实际存储的数据）、一个前驱指针和一个后驱指针。

## queue
> 只能访问 queue<T> 容器适配器的第一个和最后一个元素。只能在容器的末尾添加新元素，只能从头部移除元素。

## deque
> 双端队列（deque）是一种支持向两端高效地插入数据、支持随机访问的容器。双端队列的数据被表示为一个分段数组，容器中的元素分段存放在一个个大小固定的数组中，此外容器还需要维护一个存放这些数组首地址的索引数组，如下图所示。

## array
> c++中的数组类型是继承了c语言的特性，在使用数组的时候要注意数组越界操作问题。为了更安全的对数组进行操作，c++提出了数组模板类array。

## set
> 除了没有value，set 容器和 map 容器很相似

## stack
> Stack(栈)是一种后进先出的数据结构，也就是LIFO(last in first out) ，最后加入栈的元素将最先被取出来，在栈的同一端进行数据的插入与取出，这一段叫做“栈顶”。栈的内部实现方式，比如vector，deque，list。如果不指明它，默认使用deque(双端队列)。

----------
# 面试题

## C++11 的原子变量
`#include <atomic>`

## 移动语义、完美转发



