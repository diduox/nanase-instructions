### Makefile简易入手教程

#### Build

构建是指将源代码和资源文件转换成可以运行的应用程序的过程。

构建通常会包括编译，链接，打包和部署的步骤。

![](./images/Build.png)



当只有一个源文件的时候，我们可以直接使用一些编译器的指令，来手动完成构建的过程。

```shell
gcc helloworld.c -o helloworld
javac helloworld.java
```

但是当项目变得复杂时，手动编译就会变得非常复杂。

#### MAKE

为了演示make的使用方法，我们现在新建一个头文件**``message.h``**

```c
void message();
```

然后再来添加一个**`message.c`**的源文件，来实现这个函数

```c
#include <stdio.h>

void message(){
    printf("这是一个背叛！\n")；
}
```

最后再写一个main文件

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include "message.h"

int main(){
    message();
    return 0;
}
```

##### 如何使用Make和Makefile构建文件？

Make是通过读取一个叫做Makefile的文件中定义的规则，来执行构建的过程的。

```makefile
target : dependencies
	action
	
#	makefile的规则
#目标文件 : 依赖文件
#	要执行的命令
```

Makefile文件前面的`M`大写小写均可，后面不能有扩展名。

```makefile
hello: main.c message.c
	gcc main.c message.c -o hello
#命令前面必须是tab键，不能是空格，否则就会报错。
```

这个Makefile的意思是，如果我们要生成`hello`这个可执行文件的话，它会先检查`main.c`和`message.c`这两个文件是否有更新，如果有更新的话，就会执行下面这行命令，来编译这两个文件。

输入make即可进行编译。

当我们再次执行make的时候，make会检查目标文件和依赖文件的时间戳，如果**依赖文件的时间戳比目标文件新**，也就是**依赖文件有更新的话，才会执行命令来重新生成目标文件**，否则就会跳过这个规则，而不执行任何操作。

```shell
#再次使用make
make: 'hello' is up to date.
#可以使用touch来更新文件的时间戳
touch main.c
```

##### 更好的写法

```makefile
hello: main.c message.c
	gcc main.c message.c -o hello
#因为我们把两个文件写到了一行 所以其中只要有一个文件更新，就要进行重新编译
```

在实际的项目中，更加规范和实际的做法，是把编译和链接分开来写。

```makefile
hello: main.o message.o
	gcc main.o message.o -o hello

main.o: main.c
	gcc -c main.c

message.o: message.c
	gcc -c message.c
	
#这样的话只有经过修改的文件，才会重新进行编译	
```

##### **伪目标（.PHONY）**

###### clean

伪目标就是一个不生成文件的目标，它只是一个标签，用来执行一些操作。

最常用的伪目标就是clean，用来清理编译过程中生成的临时文件或者可执行文件。

```makefile
clean: 
	rm -f *.o hello
#删除.o为后缀的文件和hello文件
```

这样就可以清理掉编译过程中生成的所有文件。

**但是！**

如果我们本地目录下有一个叫做`clean`的文件的话，那么`make clean`这个命令就会失效，因为make会把`clean`当作文件来处理，Make会提示我们这个文件已经是最新的了，而不执行操作。

为了避免这种情况，可以前面加上一个`.PHONY`，来显式的告诉`Make`这个`clean`就是一个伪目标，这样Make就不会把clean当成文件名来处理了。

```makefile
.PHONY: clean
```

###### all

```makefile
.PHONY: all
```

当我们执行Make的时候，Make会默认执行第一个规则，而不会非必要的向下执行。（如果clean是第一个就会只执行clean。）

但是如果我们想要生成的目标文件不止一个的话，就可以使用`all`这个伪目标。

```makefile
.PHONY: clean all
all: hello world
	echo "all done"

hello: main.o message.o
	gcc main.o message.o -o hello

world: main.o message.o
	gcc main.o message.o -o hello

main.o: main.c
	gcc -c main.c

message.o: message.c
	gcc -c message.c

clean: 
	rm -f *.o hello
```

all这个伪目标也经常用来指定默认的目标，如果我们把all这个伪目标放在第一行，当我们执行make的时候，就会默认生成所有的目标文件。

##### 编译单独文件

```makefile
make <filename>
```

##### 相同的依赖文件和生成规则

如果两个文件生成所需要的依赖文件和生成规则都是一样的，就可以把它们写在一行，中间用空格隔开即可，同时更改一下生成文件。

```makefile
hello world: main.o message.o
	gcc main.o message.o -o $@
#$@代表目标文件
```

##### 关闭命令行输出

默认的情况下，Make会把执行的命令行打印输出到终端上，若不想输出命令行的话，可以在命令前加入一个@符号。

```makefile
all: hello world
	@echo "all done"
```

##### 定义变量

在编译的时候，经常会用到一些编译选项，可以将这些编译选项定义成一个变量。

```makefile
CFLAGS = -Wall -g -O2
#-Wall 告诉编译器显示所有的警告
#-g 用于在编译时添加调试信息
#-O2 指定优化级别
```

下面的编译命令里面就可以直接使用这些变量了。

```makefile
.PHONY: clean all
CFLAGS = -Wall -g -O2
all: hello world
	@echo "all done"

hello world: main.o message.o
	gcc $(CFLAGS) main.o message.o -o $@

main.o: main.c
	gcc $(CFLAGS) -c main.c

message.o: message.c
	gcc $(CFLAGS) -c message.c

clean: 
	rm -f *.o hello
```

> 在vscode中，使用alt+鼠标左键，可以同时设置多个光标。

在正式的Makefile中，一般也会把目标文件、源文件、中间文件都定义成变量。

```makefile
CFLAGS = -Wall -g -O2
targets = hello world
sources = main.c message.c
objects = main.o message.o
```

##### 自动变量

```makefile
$@
#目标文件
$<
#第一个依赖文件
$^
#所有的依赖文件
```

```makefile
.PHONY: clean all
CFLAGS = -Wall -g -O2
targets = hello world
sources = main.c message.c
objects = main.o message.o

all: $(targets)
	@echo "all done"

$(targets): $(objects)
	gcc $(CFLAGS) $(objects) -o $@

main.o: main.c
	gcc $(CFLAGS) -c $<

message.o: message.c
	gcc $(CFLAGS) -c $<

clean: 
	rm -f *.o hello
```

##### 通配符

```makefile
%.o: %.c
	gcc $(CFLAGS) -c $<
#代表所有对应的.o文件都是由对应的.c文件生成的
# %替代零个或多个字符
```

##### 参数

```makefile
make <命令> -f <makefile的文件名>
```

可以通过-f参数来指定使用的`makefile`文件。

```makefile
make -n
```

可以显示出make要执行的命令，而不会真正的执行。

```makefile
make -C
```

用来指定`makefile`执行的目录。

> 在file文件夹中，有jyy上课用的makefile文件
>
> 也就是因为这个文件，我才花了几个小时学了一些makefile