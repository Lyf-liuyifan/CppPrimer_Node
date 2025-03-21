

# C++ Primer 阅读补充

​	笔记主要是关于使用C++途中所需要注意的细节，以及对C++Primer的补充

## 2.变量和基本类型

### 2.2变量

#### 2.2.1变量定义

​	变量的定义初始化不能进行隐式转换

#### 2.2.2变量声明和定义的关系

​	如果想声明一个变量而不是声明一个变量，可以使用extern关键字来声明

```cpp
extern int i;//声明
extern int i = 100;//初始化抵消了，这是定义
```

#### 2.2.4名字的作用域

​	当内层作用域与外层作用域名字重名，那么外层的名字会被隐藏，这时候要访问外层的名字得加作用域，比如访问全局的得加==：：==。

​	还有就是有个需要注意的点就是for，if等函数就算没有写大括号，后面一条语句也是在其作用域内的。

### 2.3复合类型

#### 2.3.1引用

​	引用赋值引用就等于变量赋值给引用，没什么区别，就是普通传递值。

​	引用不能用字面值去初始化，只能是对应类型的变量，只有初始化之后才能进行隐式类型转换。

#### 2.3.3理解复合型声明

​	只有int类型没有int*或者int&类型所以

```cpp
int* i, j;//i为指针类型，j为int型
const int i = 2, j = 3;//const是跟类型的，这说明j也是一个不可修改的变量
```

#### 2.4.2指针和const

​	指针和引用的const的意思只是不能通过指针和引用去修改值而不是说地址上的值不能改变。

#### 2.4.3顶层const

​	**1.顶层const:对象本身不可以改变：**

- 定义：const直接修饰变量本身，表示改变量的值无法被修改

  ```cpp
  const int i = 0;//i是顶层const
  ```

- 特点：

  无论变量是什么基本类型，还是指针，只要修饰本身就是顶层const。

**2.底层const：指针/引用指向的对象不可变**（只有指针或引用才有这特性

​	拷入拷出的对象必须拥有相同的底层资格。或者另个对象的数据类型可以相互转换。一般来说，==非常量可以转换成常量==。顶层const的拷贝是不受影响的。值得注意的是：一般是看被拷贝是否是底层const。

```cpp
const int ci = 42;
int i = 0;
i = ci//拷贝ci，因为ci是顶层const所以和普通变量没区别
const int* p2 = &ci;//p2 是底层const
const int* const p3 = p2;
p2 = p3;//底层类型相同，顶层不受影响
int* p = p3;//p3有底层const 所以这条语句是错误的
p2 = p3;//right
p2 = &i;//正确 int* 可以转换成const int*
int& r = ci;//ci有底层所以不行
const int& r2 = i;//可以因为非常量可以转换成非常量
```

## 3.字符串、向量和数组

### 3.2标准库类型string

#### 3.2.2string对象上的操作

​	stringt头文件里的getline函数可以读取输入的一整行，包括空白，他是遇到换行符(默认以换行符为分隔符）才停滞的，但是换行符不读到字符串里，但是读入缓冲区的换行符会被读取且丢掉。

```cpp
template< class CharT, class Traits, class Allocator >
std::basic_istream<CharT, Traits>&
getline( std::basic_istream<CharT, Traits>& input, std::basic_string<CharT, Traits, Allocator>& str );
```

### 3.3标准库类型vector

#### 3.3.3其他vector操作

**计算vector内对象的索引：**

​	C++的字符串和C语言风格的字符串不同，C语言的字符串他含有'\o'，string却不含这个，所以对空string的索引是错误的

```cpp
string s;
s[0] = 1;//错误
```

### 3.5数组

#### 3.5.1定义和初始化内置数组

​	再次强调一下，常量表达式不是所谓的常量，而是在编译期间就能确定的值，比如用const声明的变量，或者是字符串常量。constexpr变量用来由编译器验证变量的值是否是常量表达式（感觉就和const是一个差不多的效果。

​	数组的维度里面必须是常量表达式

```cpp
int int_arr[常量表达式]；
```

**初始化**

​	值得注意的是全局的内置类型数组声明时会初始化为默认值，而在函数内不显示初始化，是不会初始化的

```cpp
int a[10];//a中元素初始化为0
int main(){
	int b[10];//不会默认初始化，值为栈中垃圾值
}
```

这样的原因是全局的数组是存储在静态存储区的，编译期间就能确定分配大小，为了确保程序的可确定性，编译器会主动初始化该值。
但是在函数内部的，首先我们不知道函数会调用多少次，我们频繁的分配和释放栈内存，会影响效率，所以我们把这个任务交给程序员自己。

#### 3.5.5与旧代码的接口

**使用数组初始化vector对象**

```cpp
int arr_i[] = {0,1,2,3,4};
vector<int> v(begin(arr_i), end(arr_i));
vector<int> v1(arr_i, arr_i + 5);
```

==这种数组拷贝给vector的方式也很有意思，这里的指针被隐式转换成了迭代器。==

```cpp
template< class InputIt >
vector( InputIt first, InputIt last,
        const Allocator& alloc = Allocator() );
```

用的是这个构造函数。

### 3.6多维数组

三种循环:

```
int a[3][4] = {{...}, {...}, {...}};
//for循环
for(int i = 0; i < 3; i++){
	for(int j = 0; j < 4; j++){
		cout << a[i][j] << endl;
	}
}
//范围for循环
for(auto & i : a){
	for(auto & j : i){
		cout << j << endl;
	}
}
//用指针
for
```

## 6.函数

### 6.2参数传递

#### 6.2.3const形参和实参

---

**补充：有无const的影响**

一、值传递的时候

```cpp
void func(int x){}
const int a = 10;
func(a);//允许，因为值传递不会隐式改变传入参数的性质，我改变形参值不会影响实参值
```

二、指针/引用传递时

1.实参是const, 形参是非const

```
void func(int* ptr){}
void func2(int& ref){}

const int a = 10;
const int* p = &a;
func(p); //错误，因为这样传入就可以通过ptr或者ref改变传入参数的值，这样就不符合常理，所以会报错
func2(a); // 错误，因为这样传入就可以通过ptr或者ref改变传入参数的值，这样就不符合常理，所以会报错
```

- 报错原因：C++禁止隐式去除const

2.实参是非const， 形参是const

这种是可行的，编译器允许隐式添加const（保护升级，不会导致数据被意外修改）

三、最佳办法

1.形参尽量用const

2.避免强制去除const

---

#### 6.2.6可变参数的函数

​	当所有的实参类型相同时，可以传递一个名为initializer_list的标准库类型（头文件也叫这个）；如果实参类型不同，我们编写一种特殊的函数，也就是所谓的可变参数模板。

1.initializer_list和vector不一样，==他里面的函数永远都是常量==，是不可变的。

2.省略符形参

​	这种功能仅用于与c函数交互的程序中， 省略符形参仅仅用于c和c++通用的类型，如int,double。

### 6.3返回类型	

#### 6.3.3返回数组指针

​	返回数组指针就和声明数组指针是一样的

```cpp
Type (*funcNmae(param))[];
```

值得注意的是返回数组指针的大括号内的参数是必须写的，因为这必须要编译期间明确的。

## 8.IO库

### 8.1关于Cin

#### 8.1.1Cin怎么读取输入的

​	我们如果不知道cin到底怎么读取输入的话，会造成很多意外的情况和bug，比如当Cin要把输入读取到char类型当中的时候，他并不会忽略开头的换行符，假如我们前一次输入了回车，下一次读取输入就会把换行读进去，这并不是我们想看到的。

​	其他的读入都是会忽略空白符（空格， 换行符， 制表符

- Windows允许通过键盘模拟文件尾：Ctrl+Z

#### 8.1.2Cin作为判断条件

​	不仅Cin可以在while中做为循环的判断条件，在for循环中也可以，只要是bool类型的都可以，要学会灵活运用这点，可以做出更优美的代码。

```c++
for(Sales_item item;std::cin >> item; std::cout << item);
```

## 10.泛型算法

#### 10.3.3lambda表达式捕获和返回

​	lambda表达式的返回值类型可以不显示指定，可以隐式指定，但是这有个条件就是函数体只能有明确的返回类型，c++11之后只能有一个返回语句，C++14后可以有多个返回语句，但是也只能是一个类型的返回值。



## 16.模板与泛型编程

### 16.1定义模板

### 16.1.1函数模板

**模板类型参数**

​	模板类型参数用于指定返回类型或函数的参数类型，以及在函数体内用于变量声明或==类型转换==的

​	**有个值得注意的就是：**新手容易把其他模板当做类型，我们的模板参数类型只能是vector<int>这类明确的类型，而不能是模板。
