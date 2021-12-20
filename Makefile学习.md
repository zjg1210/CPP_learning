Makefile学习：
    一个工程中的源文件不计数，其按类型、功能、模块分别放在若干个目录中，makefile定义了一系列的规则来指定，哪些文件需要先编译，哪些文件需要后编译，哪些文件需要重新编译，甚至于进行更复杂的功能操作，因为makefile就像一个Shell脚本一样，其中也可以执行操作系统的命令。
make是一个命令工具，是一个解释makefile中指令的命令工具。

1、规则：
   三要素 ：目标、依赖、命令
   格式：
   目标：依赖（各个.c文件）
         命令 
target... : prerequisites ...

          command

          ...

          ...
         -------------------------------------------------------------------------------
         
         
         

       target也就是一个目标文件，可以是Object File，也可以是执行文件。还可以是一个标签（Label），对于标签这种特性，在后续的“伪目标”章节中会有叙述。

       prerequisites就是，要生成那个target所需要的文件或是目标。

       command也就是make需要执行的命令。（任意的Shell命令）
target这一个或多个的目标文件依赖于prerequisites中的文件，其生成规则定义在command中。说白一点就是说，prerequisites中如果有一个以上的文件比target文件要新的话，command所定义的命令就会被执行。

在看别人写的Makefile文件时，你可能会碰到以下三个变量：$@，$^，$<代表的意义分别是： 

                   他们三个是十分重要的三个变量，所代表的含义分别是：

                   $@--目标文件，$^--所有的依赖文件，$<--第一个依赖文件。
                   
     当有多个文件需要编译时，Makefile里面的第一行必须是你的终极目标命令，后面拆开生成的目标都是为了终极目标的依赖文件
     如：(版本1）
     app:main.o add.o sub.o                     (终极目标）
         gcc main.o add.o sub.o -o app
     main.o:main.o
            gcc -c main.c
     add.o:add.o
            gcc -c add.c
     sub.o:sub.o
            gcc -c sub.c
            
      上述就可以：当某个文件修改了之后，输入make命令，系统就会自动检查目标文件的时间序列，目标文件的时间一定是比依赖文件的要晚的，因此，当次要目标文件发现所依赖的文件时间要比自己的还要
      晚，则进行编译gcc命令，更新次要目标文件，这时最终目标文件检查到依赖文件的时间比自己的还要晚，则进行编译。
            
      上述还可以进行优化：  （版本2）
      obj=:main.o add.o sub.o   //自定义变量
      target=app
      $(target):$(obj)
          gcc $(obj) -o $(target)
          
      %.o:%.c    //当目标文件进行执行时，发现次要目标文件中，没有发现，则将依赖文件名填充到%
         gcc -c $< -o $@
         
         $@--目标文件，$^--所有的依赖文件，$<--第一个依赖文件
         
       优化版本3：看起来更高大上
       obj=main.o add.o sub.o
       target=app
         #makefile中自己维护的变量
       CC=gcc
       CPPFLAGS = -I      // 预处理器需要的选项
       $(target):$(obj)
            $(CC) $(obj) -o $(target)
            
       %.0:%.c
            $(CC) -c $< -o $@
       /// vi 中替换操作 ：行号+s/被替换的名称/要替换成的名称 
       
 2、Makefile函数
       优化版本4：
        #obj=main.o add.o sub.o
       target=app
         #makefile中自己维护的变量
         #Makefile中的函数的使用
       src=$(wildcard ./*.c)    #找出所有.c的文件
       obj=$(patsubst ./%.c, ./%.o, $(src))   #将.c（从src中）的文件代替.o文件
       CC=gcc
       CPPFLAGS = -I      // 预处理器需要的选项
       $(target):$(obj)
            $(CC) $(obj) -o $(target)
            
       %.0:%.c
            $(CC) -c $< -o $@
            
       .PHONY：clean  #PHONY伪目标，当执行时，不用在进行比较clean    
       clean:
            rm $(obj) $(target) -f(强制执行)     #删除所有.o的文件以及目标文件app  当每次点make命令时都会重新编译app
 
       
          
            
                   
                   
                   
                   
