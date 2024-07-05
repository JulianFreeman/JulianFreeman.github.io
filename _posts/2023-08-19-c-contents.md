---
title: C 语言杂记
date: 2023-08-19 17:11:00 -0400
categories: [编程笔记, C]
tags: [programming, c]
description: 本文记录了一些自己对 C 语言的理解
---

> 以下代码测试环境，无特殊说明，皆为 Visual Studio Community 2022 x86 on Windows 64
{: .prompt-tip}

> **VS 回车自动填充代码的设置：**  
工具-选项：  
文本编辑器-C/C++-高级-IntelliSense  
主动提交成员列表：True
{: .prompt-tip}

## 1. void 作为参数

对于 C 来说，如果一个函数的参数列表为空，这表示该函数可以接收任意数量的参数，如果一个函数的参数列表只有一个 `void` ，那表示该函数不需要任何参数。

但是在实际测试中，对于参数列表为空的函数，不管调用时传不传参，程序都能正常运行，且不会有警告；对于参数列表只有一个 `void` 的函数，调用时不管传不传参，程序都能正常运行，但是如果调用时传参编译器会给出一个警告，说这个函数的参数列表是 `void` ，意思就是不用传任何参数。

所以 **对于 C 来说，如果参数列表为空，可以加一个 `void` 显式声明，但是不加也一样。**

但是对于 C++ 来说就不是这样了，对于 C++ 来说，参数列表为空或者只有一个参数 `void` ，它们的意思是一样的，就是指这个函数不需要传任何参数，如果你非得传，那就报错，程序无法正常运行。

所以 **对于 C++ 来说，如果参数列表为空，就留空就行**，跟 Java 、 Python 啥的是一样的。

```c
#include <stdio.h>

int main(void) {
    printf("Hello world\n");
    return 0;
}
```
{: file="hello.c"}

```cpp
#include <iostream>

int main() {
    std::cout << "Hello world" << std::endl;
    return 0;
}
```
{: file="hello.cpp"}

## 2. 查看整数类型的最大最小值

包含头文件 `limits.h` ，就可以查看整数类型的数值范围了。

参考文档：[C 和 C++ 整数限制](https://learn.microsoft.com/zh-cn/cpp/c-language/cpp-integer-limits?view=msvc-170)

对于数字类型，可以这样记忆， `short` 是两个字节， `long` 是四个字节， `long long` 是八个字节，这些是固定的（应该是吧？）。对于 32 位以上的机器， `int` 跟 `long` 是相同的，也是四个字节，在 32 位以下的机器（可能现在已经见不到了）， `int` 跟 `short` 相同，是两个字节。

因为现在基本见不到 32 位以下的机器了，所以可以认为，**`int` 类型就是四个字节**。但因为 `long` 也是四个字节，所以 `int` 就把 `long` 代替了，如果数字的大小超过了 `int` 的范围，接下来就得用 `long long` 来表示了。同样，对于 `int` 和 `long` ，占位符都使用 `%d` 就可以，而对于 `long long` ，需要使用 `%lld` 。

```c
#include <stdio.h>
#include <limits.h>

int main(void) {
    printf("int max: %d\n", INT_MAX);
    printf("int min: %d\n", INT_MIN);
    
    printf("long max: %d\n", LONG_MAX);
    printf("long min: %d\n", LONG_MIN);

    printf("long long max: %lld\n", LLONG_MAX);
    printf("long long min: %lld\n", LLONG_MIN);

    return 0;
}
```

## 3. 关于字符串

```c
#include <stdio.h>
#include <string.h>

int main(void) {
    char* name = "John Smith";
    printf("%c\n", name[1]);
    //name[1] = 'O';

    char address[9] = "New York";
    printf("%c\n", address[1]);
    address[1] = 'E';
    printf("%s\n", address);

    char subject[8] = {'E', 'n', 'g', 'l', 'i', 's', 'h', '\0'};
    printf("%s\n", subject);
    printf("Length of address: %u\n", strlen(address)); //8
    printf("Length of subject: %u\n", strlen(subject)); //7

    return 0;
}
```

### 创建字符串

有两种创建字符串的方式，第一种是 `char* name = "John Smith";` ，第二种是 `char address[9] = "New York";` ，当然这里的 `9` 可以省略，不过为了解释后面的终止符的问题还是保留它。

众所周知， C 语言中没有字符串这种类型，所谓的字符串实际是字符数组，所以要创建字符串按理说比较好理解的方式是这样 `char subject[8] = {'E', 'n', 'g', 'l', 'i', 's', 'h', '\0'};` 。明摆的字符数组。但是好处是，因为字符串用的太过频繁，所以可以通过 **字符串字面量** 的方式快速创建字符串，也就是形如 `"Hello world"` 这样的东西。上述的第一种和第二种创建字符串的方式都是通过字符串字面量来创建的，但是它们有什么区别呢？

目前来说，区别是这样的，第二种方式创建的字符串实际上还是个字符数组，所以它 **允许你通过取索引的方式修改字符串中的某个字符**。但是通过第一种方式，也就是指针的方式创建的字符串是不可变的，也就是 **你不能通过取索引的方式修改字符串**，虽然编译器不报错，但程序会异常退出的。

目前先这样说吧。

### 字符串终止符

那么另一个问题，为什么 `"New York"` 看上去只有 8 个字符，却创建了一个 9 个字符大小的数组？为什么 `subject` 的最后一个字符是 `'\0'` ？这就是终止符的问题了。

因为字符串实际是存储在内存里的一串字符，在它的前后是其它的地址空间，存储着其它的一些东西，当我们输出字符串时，程序并不知道这个字符串在哪里结束，所以它需要一个终止符做标记。这个终止符就是 `'\0'` 。只要程序在输出字符串时遇到了 `'\0'` ，它就认为字符串到这里结束了，就不再管后面的内容了。

所以，对于字符数组，也就是形如 `subject` 变量创建的方式，我们需要显式把最后一个字符指定为终止符，表示这里就是字符串的结束。同时终止符本身也占一个字符，所以字符数组的总长度是 `8` 。但是对于字符串字面量，它会自动帮我们在字符串结尾追加一个终止符，哪怕我们不显式地写出来，所以对于 `"New York"` ，虽然面上看字符只有 8 个，但是因为编译器会追加一个终止符，所以需要 9 个长度的数组才能存下。

如果我们执意给数组减少一个长度会怎么样？

对于字符数组，编译器会直接报错，因为它发现声明的数组长度跟实际填充的字符个数不符，所以就直接阻止你运行程序了。对于字符串字面量，编译器不会报错，但是字符串的最后一个字符就不再是终止符了，可以理解为被超过规定长度的字符覆盖了。这时程序会持续输出字符，直到找到内存中的下一个 `'\0'` 。

最后，再说一下 *字符串的长度* 这个东西。字符串的长度跟声明字符数组时规定的长度并不是一回事，至少直觉上来说，字符串的长度中不应该包含终止符这个东西。实际上也是这样的，使用 `strlen` 函数求字符串长度的时候，不会包括终止符本身。

不过另一点有意思的是， `strlen` 函数的返回值类型是 `size_t` ，这个类型在 32 位系统下是 `unsigned int` ，而在 64 位系统下是 `unsigned __int64` ，而 `__int64` 是一个 8 字节长的类型。所以如果在 64 位系统中运行上述代码，占位符就得用 `%llu` 。

### `char*` 和 `char[]`

所以回到最开始，使用指针创建字符串和使用字符数组创建字符串有什么区别呢？

运行如下代码：

```c
#include <stdio.h>

int main(void) {
    char* name = "John";
    char** p_name = &name;
    printf("Address of   name: %p\n", name);
    printf("Address of  &name: %p\n", &name);
    printf("Address of p_name: %p\n", p_name);

    return 0;
}
```

其中 `&name` 就是 `p_name` 。但是我们可以看到，**`name` 和 `&name` 这两个地址是不一样的**。

运行如下代码：

```c
#include <stdio.h>

int main(void) {
    char name[5] = "John";
    char (*p_name)[5] = &name;
    printf("Address of   name: %p\n", name);
    printf("Address of  &name: %p\n", &name);
    printf("Address of p_name: %p\n", p_name);

    return 0;
}
```

同样， `&name` 就是 `p_name` ，这里为了演示一下怎么取数组的指针。但是我们能看到，这三个输出是一样的，也就是 **`name` 和 `&name` 是一样的**。

也许这样可以解释一点两者的区别，但是更细节的还是等我理解一下 C 的变量到底是什么东西之后再说吧。

补充一点，这里 `char (*p_name)[5]` 只是一种 **声明数组指针变量** 的写法，并非是声明数组，所以我们可以给它赋值 `&name` ，但不能赋值 `name` ，也不能赋值 `{'J', 'o', 'h', 'n', '\0'}` 之类的。

## 4. 变量是什么？

C 中的变量是什么？跟 Python 中的变量有什么不同？我们来说一下这个问题。

首先， C 中的变量跟 Python 中的变量肯定是不同的。先解释 Python 中的变量。

### Python 中的变量

Python 中的变量就相当于一个标签，当我们写下了这样一行代码 `a = 65537` 之后，实际上代码背后的故事是这样的：程序申请一块内存空间，把值 `65537` 存进去，然后贴上一个标签 `a` 。

我们用下面的代码解释：

```python
a = 65537
b = a
a = 100000
print(b)  # still 65537
```

这段代码发生了如下几个故事：

1. 申请一块内存空间，把值 `65537` 存进去，贴上标签 `a` 。
2. 把值 `65537` 贴上另外一个标签 `b` 。此时值 `65537` 同时有两个标签。
3. 申请一块内存空间，把值 `100000` 存进去，然后把标签 `a` 从值 `65537` 上撕下来，重新贴到值 `100000` 上去。
4. 此时的情况是这样的：有一个值 `65537` ，上面有一个标签 `b` ；有一个值 `100000` ，上面有一个标签 `a` 。

所以其实 Python 中的变量没有类型，它们可以被贴到任意类型的值上，也就是说值有类型，但变量没有，变量只是值的一个标签。

### C 中的变量

再来看看 C 中的变量。

C 中的变量跟 Python 中的完全不是一回事。C 中的变量并不是标签，而是一块内存空间。当我们写下这样一行代码 `int a = 65537` 之后，代码背后的故事是这样的：申请一块四字节长的内存空间，标记这段空间为 `a` ，然后把 `65537` 填充到这段空间中。

用代码来解释：

```c
int a = 65537;
int b = a;
a = 100000;
printf("%d\n", b); //still 65537
```

虽然结果跟 Python 中的一样，但故事是完全不一样的：

1. 申请一块四字节的空间，标记为 `a` ，然后把 `65537` 填充到 `a` 中。
2. 再申请一块四字节的空间，标记为 `b` ，然后把 `a` 中的内容拷贝到 `b` 中，也就是把 `65537` 填充到 `b` 中。此时有两块 **不同的** 内存空间，分别为 `a` 和 `b` ，其内容都是 `65537` 。
3. 把 `a` 的内容修改为 `100000` 。
4. 此时的情况是这样的，还是有两块不同的内存空间，分别为 `a` 和 `b` ，前者的内容为 `10000` ，后者的内容为 `65537` 。

### 区别

注意到区别了吗？在 Python 中，变量是值的标签，在 C 中，值是变量的内容。

其实最明显的不同在第二步，当完成第二步的时候，在 Python 中，只有一个值 `65537` 和两个标签 `a` 和 `b` ；而在 C 中，有两个 `65537` ，分别在两个不同的内存空间 `a` 和 `b` 中，它们彼此没有任何关系，只是恰好值相同而已。

顺便说一下，在 C 中，赋值就是拷贝，不管是多大的类型，都是拷贝，同时函数传参也是拷贝，不管传的是一个多大的对象，都是拷贝。

## 5. 补充字符串的创建以及延申打印的规则

所以 `char* name = "John";` 和 `char name[5] = "John";` 到底有什么区别？我们可以先看一些细节，再回过头看第 3 节中的代码。

### 背后的故事

当我们写下一句 `char* name = "John";` 的时候，发生了如下的故事：

1. 在内存中申请一块四字节的空间，标记为 `name` ，因为 `name` 是一个指针，记录的是一个地址，在 32 位的机器中，地址就是四字节的。我们假设这块空间的地址分别是 `0x10101010` 、 `0x10101011` 、 `0x10101012` 、 `0x10101013` 。
2. 然后在另一块位置，申请五个字节的空间，分别填充 `J` 、 `o` 、 `h` 、 `n` 、 `\0` 。我们假设这块空间的地址分别是 `0x20304050` 、 `0x20304051` 、 `0x20304052` 、 `0x20304053` 、 `0x20304054` 。
3. 把第一个字符的地址 `0x20304050` 填充到 `name` 中，按照小端的存储法，也就是把 `0x50` 存储到 `0x10101010` 中， `0x40` 存储到 `0x10101011` 中，以此类推。
4. 所以此时，如果我们按地址打印 `name` ，那么得到的结果就是 `0x20304050` ，因为这就是 `name` 里的内容（回看第三步， `name` 就是 `J` 的地址）。但是如果我们取 `name` 的地址，也就是 `&name` ，那么得到的结果就是 `0x10101010` ，因为这是 `name` 的地址。

再来看看写下一句 `char name[5] = "John";` 的时候，发生了什么：

1. 在内存中申请一块五个字节的空间，标记为 `name` 。我们假设这块空间的地址分别是 `0x30303030` 、 `0x30303031` 、 `0x30303032` 、 `0x30303033` 、 `0x30303034` 。
2. 将 `J` 、 `o` 、 `h` 、 `n` 、 `\0` 分别填充到这五个字节的空间中。
3. 所以此时如果按地址打印 `name` ，结果就是 `0x30303030` ；如果取 `name` 的地址，也就是 `&name` ，结果还是 `0x30303030` ，因为本身就是 `name` 的地址。

这时候再回看第 3 节中的代码，是不是结果就对上了。

这里可能有点困惑的地方，是为什么按地址打印 `name` 的时候出现的结果是 `name` 的地址呢？注意，**其实不管按什么格式打印变量，打印的都应该是变量的内容**，比如如下代码：

```c
int age = 20;
printf("%p\n", age); //00000014
```

当我们按照地址打印 `age` 时，打印的并不是 `age` 的地址，而是把 `age` 的内容转换成地址的格式来打印。就如同在分析 `char* name = "John";` 的时候，当我们按地址打印 `name` 时，打印的并非 `name` 的地址，而是 `name` 的内容，恰好这个内容就是一个地址，所以正好可以按地址打印。而当我们打印 `&name` 的时候，打印的才是 `name` 的地址。

但是在分析 `char name[5] = "John";` 的时候，貌似有点不同，貌似是理论上无法把一个字符串当成地址来打印，所以就打印了 `name` 的地址，同时当我们打印 `&name` 的时候，打印的还是 `name` 的地址。

### 另外的猜想

这其实应该是数组的问题。因为经过测试，上面的结论（不管按照什么格式打印变量，打印的都应该是变量的内容）还是正确的，**对于字符、整数、指针都是这样的**。虽然指针一般是通过取变量地址定义的，但是因为本质上指针只是一个整数，所以也可以直接赋值一个整数，只不过这样无法解引用而已。甚至对于结构来说，也是这样的，因为结构只是其内部各种类型的变量的一个简单排列（也许之后会说到结构），如果要按照一定格式打印结构，其实打印的是结构的第一个变量。

在这之中，字符串有点特殊，数组也有点特殊。

字符串特殊在，我们并不是直接把字符串的内容填充到变量中的（但对于字符、整数、指针都是这样的），而是先创建一个字符指针变量，再创建一个字符数组，然后把字符数组的第一个字符的地址填充到字符指针变量中。

数组特殊在，数组变量表现得像个指针，但又不是指针。当我们用地址格式或者整数格式打印数组变量时，并不是把数组变量的内容转成地址或整数，而是把数组变量的地址转成地址或整数了。也就是说，**当我们打印数组变量时，并非打印的数组变量的内容，而是打印的数组变量的地址**。

嗯……其实这里还有个特殊的东西是 `%s` 。像 `%c` 、 `%d` 、 `%p` 会分别把变量的内容当字符、整数、地址对待（不考虑数组的话），**但是 `%s` 会把变量的内容都当成地址对待**，然后取这个地址的内容，按照字符串的规则打印出来（也就是逐个取字符输出，直到找到一个终止符为止）。所以如果你运行下面的代码，肯定是不行的：

```c
int a = 97;
printf("%s\n", a);
```

因为 `%s` 会把 `a` 的内容当地址对待，也就是把 `97` 当地址对待，然后取 `0x00000061` 这个地址上的内容，肯定是取不到的，因为大概率这块地址不属于分配给程序的内存范围。

但是如果你运行这个：

```c
int a = 97;
printf("%s\n", &a); //a
```

结果是可以运行的，也能正常输出，除了会有警告之外。我们假设 `a` 的地址是 `0x10101010` ，也就是变量 `&a` 的内容，所以因为 `%s` 会把变量的内容当地址对待，恰好 `&a` 的内容就是一个地址，也就是 `0x10101010` ，所以去找这个地址的内容，当成字符串输出，就找到了 `97` ，因为整数实际上有四个字节长，按小端存储法 `a` 在内存中的内容实际是 `61000000` （十六进制的小端存储），所以按照字符串输出的话刚输出一个字符 `61` （也就是 `'a'` ）就遇到 `\0` 了，所以就只会输出一个 `a` 。

再回到数组，如果要用 `%s` 的格式打印数组变量，因为打印数组变量时实际打印的是数组变量的地址，所以 `%s` 拿到了数组变量的地址，然后把它当成一个地址对待（当然这没什么区别），然后找到这个地址所存储的内容，按字符串打印出来，恰巧，这个地址存储的内容就是数组的内容（不管是什么内容，字符也好，整数也好，都按照字符串打印出来，遇到终止符就停止）。

### 总结

上面说的可能有些乱，下面总结一下。

|  | 非数组类型变量 | 数组变量 |
| --- | --- | --- |
| 非 %s 格式打印 | 把变量的内容按照相应格式打印出来 | 把变量的地址按照相应格式打印出来 |
| %s 格式打印 | 把变量的内容当成地址对待，<br>找到该地址上的内容按照字符串输出；<br>通常来说都会异常退出，因为这个地址<br>大概率不是程序可支配的内存范围<br>（创建字符指针指向的字符串除外） | 把变量的地址当地址对待（废话），<br>找到该地址上的内容按照字符串输出；<br>因为变量地址上的内容就是数组本身，<br>其实也就是把数组的内容当成字符串输出 |

举个例子：

```c
int numbers[5] = { 0x64636261, 0x68676665, 0x6c6b6a69, 0x706f6e6d, 0x00737271 };
printf("%s\n", numbers); //abcdefghijklmnopqrs
```

按照十六进制小端存储，该数组的内容分别是 `61` 、 `62` 、……、 `72` 、 `73` 、 `00` 。也就是从 `a` 到 `s` ，最后一个是终止符。

另外一个总结， `char* name = "John";` 实际上是创建一个字符指针和创建一个字符数组的结合。当按照 `%s` 打印时，按照上述总结的规则，虽然处理的是一个字符指针，但打印的正好就是 `"John"` ，而如果按照其它格式打印，那处理的就仅仅是一个字符指针的内容，也就是一个 **地址** 了（注意这个地址并非字符指针的地址，而是 `"John"` 这个字符数组的地址），打印的内容就跟 `"John"` 本身没什么关系了。

再说一点，除了在打印的时候数组变量有点奇怪之外，其它时间应该都可以把它当成一个指针看待……吧。

## 6. const 到底是啥？

### 指针变量的声明

在说 `const` 到底是啥之前，先说说指针变量到底是怎么声明的，对于 `int * age` 来说，这个星号到底是跟 `int` 一起的，还是跟 `age` 一起的？也就是说，我们是把 `int*` 看成一种新类型呢，还是把 `*age` 看成一个新变量呢？

说实话这个问题困扰了我好久，直到前几节，我在琢磨怎么声明一个指向数组的指针时，看到应该这么写： `char (*p_name)[5]` ，再加上我想起声明函数指针时也是类似的写法： `void (*func)(int)` ，我突然就感觉，看样子这个星号还是跟变量一起的啊。但是后来我发现也不然，其实这种写法只是声明指针的一种方式，并非说 `*p_name` 就等同于 `name` 。但是它对于理解 `const` 还是有帮助的。

比如说， `const int*` 到底是看成 `const (int*)` 呢，还是 `(const int)*` 呢？也就是说，到底是 `const` 修饰了一个整型指针呢，还是我们取了一个常量整型的指针呢？通过研究发现，其实应该是后者。如果按照这种写法： `const int (*p)` ，应该会好理解一点，也就是说，我们是声明了一个指向常量整型变量的指针。这是声明常量变量指针的一种方式。

### 回到 `const`

接下来会有好多代码来说明 `const` 到底是怎么回事，但是为了安全起见，我们在 C++ 环境下运行代码，因为 C++ 的 `const` 是严格的，我们后续解释这件事。

先从简单的看起吧。

```cpp
int a = 10;
const int b = a;
int c = b;
```

实际上，上述代码没有任何问题，也就是说，把变量赋值给常量，和把常量赋值给变量，都是没问题的。为什么呢？因为 C/C++ 中赋值就是拷贝，完全把右边变量的内容拷贝一份填充到左边的变量中，所以说，对于内容，也就是值来说，并没有常不常量的说法，只有变量才有常不常量的限定。比较绕，但是这么说就好理解了：**`const` 是一种限定符，用于限定变量对内容的操作**。

看例子，如果我们想修改 `b` 的内容，是不行的，因为 `const` 限定了变量 `b` 的内容不可变。但是如果我们想修改 `c` 的内容，是可以的，因为 `c` 并没有被限制。此时 `b` 和 `c` 只是有相同的内容 `10` 而已，但这两个 `10` 彼此独立，没有任何关系。

接下来把类型改成指针。

```cpp
int a = 10;
int* p = &a;
const int* q = p;
int* r = q; //error
```

上述代码中，最后一步是报错的（在 C++ 中），过不了编译器。它说我们不能用 `const int*` 的类型初始化 `int*` 的类型。此时我们可以怎么理解呢？我们再加几段代码。

```cpp
int b = 20;
q = &b;
```

上述第二行代码是没有问题的，没有任何错误。可是，你可能会问， `q` 不是被 `const` 限定了吗，为啥还能改变值呢？其实回到最开始，我们说 `const int*` 的意思是指向一个常量整型的指针，而没有说这个指针是一个常量，所以，这里的 `const` 是说，由 `q` 指向的那块内存空间被 `const` 限定了，但是 `q` 本身并没有被 `const` 限定，所以 `q` 的值可以改变。

那再看上面，为什么从 `const int*` 到 `int*` 不行呢？我们可以这样理解：**限定符只能往增多的方向转换，而不能往减少的方向转换**。细节解释我也不是很清楚，先这样理解吧。

因为有意思的是，不管是 `p` 还是 `q` ，它们指向的都是 `a` 的空间，而实际上 `a` 这块空间并没有被 `const` 限定，所以对于 `q` 来说，也 **可以认为 `const` 限定的是通过 `q` 这个变量操作 `a` 的空间时的行为**。因为同样是修改 `a` 的值， `*p = 20` 就可以成功，而 `*q = 20` 就不行。

所以 `const` 到底是啥，它到底限没限制 `q` ？限制了，又没完全限制。呵呵。

对指针来说， `const` 限制的是通过该指针变量操作其地址指向空间的行为。

1. `const` 只限制 **该变量** 的行为，对于其它变量，不管是不是指向的同一空间，也不管指向的空间有没有被限制，它不在乎。
2. `const` 没有限制改变该变量内容的行为，而是限制 **改变该变量所指向的空间的内容** 的行为。

就这样吧，再绕就更晕了。

所以如果我们就是想限制改变指针变量内容的行为怎么办？这么办：

```cpp
int a = 10;
int b = 20;
int* const p = &a;
p = &b; //error
```

上述代码最后一行会报错，因为 `p` 不可改。奇葩吧，把 `const` 写在 `int*` 右边就行了。呵呵。

你甚至可以这样：

```cpp
const int* const p = &a;
```

这样就可以达到 `p` 不可改， `*p` 也不可改了。

### 看看字符串

先上一段代码：

```cpp
const char* name = "John";
name = "Mike";
```

也许一开始你会觉得这样不行，因为 `name` 被 `const` 修饰了，但经过了上面的讨论，你大概就不会这么想了。其实上述代码完全可行， `name` 也真的被改变了。为什么？因为这里 `const` 限制的其实是通过 `name` 改变 `"John"` 的行为，而我们没有改变 `"John"` ，我们改变的是 `name` ，所以完全可以。大概你也猜到了，如果我们不想改变 `name` ，那就这样办：

```cpp
const char* const name = "John";
name = "Mike"; //error
```

另外我们怎么知道 `const` 限制的是通过 `name` 改变 `"John"` 的行为？可以看如下代码：

```cpp
char name[5] = "John";
*name = 'j'; //ok
const char* p = name;
*p = 'J'; //error
```

所以我们可以修改 `p` 的内容，却不能通过对 `p` 解引用来修改 `name` 的内容，这就是 `const` 对 `p` 的限制。

但是下面的代码不能运行：

```cpp
const char* name = "John";
const char (*p)[5] = name; //error
```

最后一行有没有 `const` 都不行。这就是说字符数组指针和字符指针还不是一回事，虽然多数时候都可以把它们当字符串看待。

### 回到 C

上述代码我们一直在 C++ 环境中运行，因为 C 中其实是允许限定符往减少的方向转换的，编译器会报警告，但并不会阻止程序运行。所以这就是为什么说 C 中的常量并非真的常量。

```cpp
const int a = 10;
int* p = &a; //error
```

上述代码在 C++ 中是无法运行的，第二行会报错。

```c
const int a = 10;
int* p = &a;
*p = 20;
printf("%d\n", a); //20
```

但是同样的代码在 C 中就可以运行，而且能成功修改 `a` 的值。虽然有警告，但是无济于事。原因在于第二行中编译器允许赋值的过程中丢掉了限定符，这一行为在 C++ 中是不允许的。

## 7. const 的进阶

先给个情景：

```cpp
int a = 10;
int* const p = &a;
*p = 20; //ok
int b = 30;
p = &b; //error
```

安全起见，我们的代码还是先在 C++ 中运行，最后再回到 C 中。

所以通过第 6 节其实我们已经理解了， `int* const p` 限制的是 `p` 而不限制 `*p` 。相反， `const int* p` 限制的是 `*p` 而不限制 `p` 。

其实也许这里我们可以总结出一个规律：忽略变量类型，如果 `const` 后面直接就是变量名（假设为 `p` ），而没有 `*` ，那 `const` 限制的就是 `p` ；如果 `const` 后面有 `*` ，然后才是变量名，那 `const` 限制的就是 `*p` 。

嗯，这样就好理解多了，尤其是对于下面的例子来说。

### 快上车，开始加速了！

从上面的代码中我们看到， `p` 是一个整型指针常量（不同于常量整型指针哈），如果我们要取这个常量 `p` 的指针怎么办？有意思的想法，其实很简单：

```cpp
int* const* q = &p;
```

看到没有，如果忽略 `const` ，其实这是一个二级指针，也很正常，因为 `p` 是一个指针，取它的地址，那不就是一个二级指针么。那么问题来了，这里的 `const` 限制的是谁？

要在之前，你可能还会懵一会，但现在就不应该懵了，因为前面已经总结规律了，这里的 `const` 后面有星有变量，所以限制的应该是 `*p` 。

```cpp
int* const m = &b;
q = &m; //ok
*q = m; //error
```

非常好，规律是成立的。也就是说，这里的 `q` 并不是一个常量，它的内容是可以改变的。

那么问题来了，如果我们想把它变成常量怎么办？呵呵，如下：

```cpp
int* const* const q = &p;
```

变态吧，这竟然没语法错误，理论上可以一直这么加下去。但这就是 C/C++，可能变态的还远不止这些呢。

回到正题，这样其实就既限制了 `p` ，也限制了 `*p` 。所以我们可以总结出另一个反向的规律：如何判断 `p` 有没有被限制？又如何判断 `*p` 有没有被限制？就往左看，如果 `p` 的左边先遇到 `const` 了，那就说明 `p` 被限制了，相反如果先遇到 `*` 了或者不再有 `const` 了，那就没有被限制。对于 `*p` 来说，忽略掉 `*` 和 `p` 之间的限定符，将 `*p` 看作一个整体，如果左边先遇到 `const` 了，那就被限制了，相反如果先遇到 `*` 了或者不再有 `const` 了，那就没有被限制。（以上都忽略了变量类型，比如 `int` ）

接下来进行一些疯狂的验证吧。

```cpp
int** const q;
```

`q` 左边是 `const` ，但 `*q` 左边是 `*` ，所以应该是限制 `q` 但不限制 `*q` 。

```cpp
int a = 10;
int* p = &a;
int** const q = &p;
int b = 20;
int* r = &b;
q = &r; //error
*q = &b; //ok
```

成立！（顺便说一下， `**q = b` 也是可以成功的，这也符合规律，因为 `**q` 左边只有 `int` 再无其它，所以不被限制）

```cpp
const int** q;
```

`q` 不限制， `*q` 不限制， `**q` 限制。

```cpp
const int a = 10;
const int* p = &a;
const int** q = &p;
const int b = 20;
const int* r = &b;
q = &r; //ok
*q = r; //ok
**q = b; //error
```

成立！

再顺便说一下，

```cpp
const int a = 10;
const int* p = &a;
const int* const* q = &p; //ok
```

这样是可以的，第三行其实是增加限定符的转换（由一个 `const` 变成两个 `const` ），所以没有问题，只不过此时 `*q` 就被限制了，即无法通过 `*q` 修改 `p` 的内容了。

### 回到 C

再回到 C 的话，就看看其实 `int* const p` 也不是完全的常量。

```c
int a = 10;
int* const p = &a;
int** q = &p;
int b = 20;
*q = &b;
printf("%d\n", *p); //20
```

可以看到，虽然 `p` 被 `const` 限制了，但 `p` 内容还是被修改了，也就是 `*q` 被修改了，其指向空间变了。问题出在第三行，这里其实是一个减少限定符的转换（由一个 `const` 变成没有 `const` ），在 C++ 里其实是不允许的，但是在 C 中只会报一个警告，并不阻止程序运行。

### 再看字符串

其实说到减少限定符，想起一点。

在 C 中，字符串字面量默认是 `char[x]` 的类型， `x` 是字符串的长度加一。而在 C++ 中，字符串字面量默认是 `const char[x]` 的类型， `x` 同上。所以如果在 C++ 中写形如 `char* name = "John";` 的代码是肯定不行的，因为这是一个减少限定符的转换。但是写成 `char name[5] = "John";` 就可以，此时数组就可以通过索引修改内容；写成 `const char name[5] = "John";` 也行，此时数组不可通过索引修改内容。

更多的内容等到研究数组的时候再说吧。

不过虽然如此啊，在 C 中，除非赋值给字符数组，否则字符串字面量也是个不可修改的内容。

## 8. 数组指针受限情况的简单说明

以下代码还是在 C++ 环境下测试，但是跟 C 环境下应该是相同的。

```cpp
int n[2] = { 1, 2 };
int m[2] = { 4, 5 };
n = m; //error
```

数组变量本身自带限定符，也就是数组变量是不可变的。但是不加 `const` 的话，数组的内容又是可变的，也就是 `n[i]` 是可变的。我们知道取索引其实跟解引用是一个意思， `n[i] <==> *(n+i)` 。所以基于这种性质，我们可以认为数组可以写成这样：

```cpp
int (*const n)[2] = { 1, 2 }; //error, just for presentation
```

当然这肯定是 **不合语法** 的，只是想这样去理解数组变量。这样的写法表示 `n` 被限制，而 `*n` 没有被限制，跟上面的表现相同。

所以如果我们要取一个数组指针怎么办？合语法的写法是这样的：

```cpp
int (*p)[2] = &n;
```

但是此时 `p` 有没有被限制？ `*p` 又有没有被限制？我们不好看，但是我们可以把它转成上面不合语法的一种写法，把隐含的 `*const` 加上，就是如下：

```cpp
int (*const *p)[2] = &n; //error, just for presentation
```

所以此时我们知道了， `p` 不限制， `*p` 限制，同时 `**p` 也不限制。

也就是说， `p = &m` 是可以的，但 `*p = m` 就不行，然而 `**p` 是什么？其实就是 `*(*p+0)` ，也就是 `*p[0]` ，也就是 `n[0]` 。所以发现了没？我们可以通过 `**p` 修改数组的第一个值，当然要修改第二个值也行， `*(*p+1)` 就可以。

所以说，给数组变量或数组指针变量的最左边（具体位置参考上述例子）加上隐含的 `*const` 就可以判断变量或指针的受限情况了。

补充一点，上面的 `p` 不受限制，如何把它限制起来？这样就行：

```cpp
int (*const p)[2] = &n;
```

注意哈，这是合语法的写法，要 **把想象的代码和实际的代码区分开**！此时我们能知道 `p` 受限，但是 `*p` 受不受限不好看，所以可以想象一下：

```cpp
int(*const *const p)[2] = &n; //error, just for presentation
```

所以， `p` 受限， `*p` 受限， `**p` 不受限。

再插一嘴，要让 `**p` 也受限也很简单， `int` 前加一个 `const` 就可以，也就相当于在数组前加一个 `const` ， `**p` 受限也就等同于不可以修改数组的内容了，完美成立！