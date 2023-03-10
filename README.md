# sylar
## 开发环境
Ubuntu20.04 
gcc 9.4.0
cmake

## 项目路径
bin -- 存放二进制
build -- 中间文件路径
cmake -- cmake函数文件夹
CMakeLists.txt -- cmake的定义文件
lib -- 库的输出路径
Makefile
sylar -- 源代码路径
tests -- 测试代码路径

## 日志系统
1)
Log4J

    Logger(定义日志类别)
       |
       | ------Formatter(日志格式)
       |
    Appender(日志输出地方)
什么是日志系统?
Log像一台全天24小时开启的摄像头，记录服务器上各种重要事务的状态，有了Log，才可以在事后快速的知道出现问题的所在。例如用户在登录失败时，Log记录下来失败时输入的账号。
什么是Log4J?
全名是Apache Log4J, 是Java程式语言的Log套件(框架)，提供方便的日志记录。
## 协程库封装

## socket函数库

## http协议开发

## 分布协议

## 推荐系统

# 开发过程中使用到的技术
## shared_ptr 智能指针
在实际的C++开发中，我们经常会碰到程序突然崩溃、所使用内存越来越大，最终不得不重启等问题，这些问题往往是由于内存资源管理不当造成的。
比如：
- 有些内存资源已经释放，但指向它的指针没有改变指向(成为了野指针)，并且后续还在使用。
    - 出现野指针的常见情况：
        - 使用未初始化的指针
        ```
        #include <iostream>
        using namespace std;
        int main() {
            int* p;
            cout << *p << endl; //编译通过，运行时出错
        }
        ```

        - 指针所指对象已经消亡(生命周期结束)
        ```
        #include <iostream>
        using namespace std;
        
        int* retAddr() {
            int num = 10;
            return &num;
        }
        
        int main() {
            int* p = NULL;
            p = retAddr();
            cout << &p << endl;
            cout << *p << endl; // runtime error：load of null pointer of type 'int'
        }
        ```
        - 指针释放后之后未置空
        
- 有些内存资源已经释放，后续还在尝试释放(重复释同一块内存会导致程序运行崩溃)。
- 没有即使释放不再使用的内存资源，造成内存泄漏，程序占用内存资源越来越多。

C++11新标准增添了unique_ptr、shared_ptr 以及 weak_ptr 这 3 个智能指针来实现堆内存的自动回收。
**什么是智能指针？**
所谓智能指针，从字面意思上来看就是“智能的”指针。其使用方法和普通指针相似，但其在适当时机可以自动的释放分配的内存。使用智能指针可以很好的避免“因为忘记释放而导致的内存泄露”的问题
> C++智能指针底层采用引用计数的方式实现的。简单的理解，智能指针在申请堆内存时，会为其配备一个整型值（初始值为1），每当有新对象使用该堆内存时，该整型值+1; 反之，每当使用此堆内存的对象释放时，该整型值-1。当堆空间对应的整型值为0时，即表明不再有对象使用它，该堆空间就会被释放掉。

**shared_ptr<T> 定义位于<memory>头文件中, 并位于std命名空间中。**

shared_ptr 和 unique_ptr, weak_ptr 不同点在于：
多个shared_ptr智能指针可以同时指向同一块堆内存空间, 由于其在实现上采用引用计数的机制，即便有一个shared_ptr智能指针放弃了对堆内存的“使用权”(引用计数-1), 也不会影响其他指向同一块堆内存的shared_ptr智能指针（只有引用计数为 0 时，堆内存才会被自动释放）。


## C++ 常见软件注释规范Doxygen

## 宏定义(带参宏定义)
无参数宏定义的格式为：`#define 标识符 替换列表`
- 使用标识符表示一常量

宏定义不是语句，是预处理指令，不能有`;`。
续行符`\`后直接按`Enter`换行，**不能**含有包括空格在内的任何字符，否则是错误的宏定义形式。
带参数的宏定义格式为：`#define 标识符(参数1,参数2,...,参数n) 替换列表`
删除宏定义的格式为：`#undef 标识符`

### 带参宏定义VS函数调用
1. 调用的发生时间：
    - 在源程序进行编译之前，即预处理阶段进行宏替换；
    - 函数调用则发生在程序运行期间。
2. 参数类型检查
    - 函数参数类型检查严格。程序在编译阶段，需要检查实参与形参个数是否相等及类型是否匹配或兼容，若有问题则会编译不通过。
    - 在预处理阶段，对带参宏调用中的参数不做检查。即宏定义时不需要指定参数类型，既可以认为这是宏的优点，即适用于多种数据类型，又可以认为这是宏的一个缺点，即类型不安全。故在宏调用时，需要程序设计者自行确保宏调用参数的类型正确。

3. 参数是否需要空间
    - 函数调用时，需要为形参分配空间，并把实参的值复制一份赋给形参分配的空间中。
    - 而宏替换，仅是简单的文本替换，且替换完就把宏名对应标识符删除掉，即不需要分配空间。

4. 执行速度
    - 函数在编译阶段需要检查参数个数是否相同、类型等是否匹配等多个语法，而宏替换仅 是简单文本替换，不做任何语法或逻辑检查。

    - 函数在运行阶段参数需入栈和出栈操作，速度相对较慢。

5. 代码长度
    - 由于宏替换是文本替换，即如果需替换的文本较长，则替换后会影响代码长度；而函数不会影响代码长度。

**故使用较频繁且代码量较小的功能，一般采用宏定义的形式，比采用函数形式更合适。** 前面章节频繁使用的getchar()，准确地说，是宏而非函数。
```c
#define getchar() getc(stdin)
```
### 关于#和##
**#的功能是 将其后面的宏参数进行字符串化操作。**


## 枚举LogLevel
**为什么在类中可以直接通过`类名::enum值`?**
该枚举是一个常量，在编译的时候已经放入到了常量区。调用的时候不需要枚举的类型也能调用。

