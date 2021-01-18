## latch 的产生和消除



### 是什么

​    锁存器是一种在异步时序电路系统中，对输入信号电平敏感的单元，用来存储信息。一个锁存器可以存储1bit的信息，通常，锁存器会多个一起出现，如4位锁存器，8位锁存器。

​     锁存器在数据未锁存时，输出端的信号随输入信号变化，就像信号通过一个缓冲器，一旦锁存信号有效，则数据被锁存，输入信号不起作用。因此，锁存器也被称为透明锁存器，指的是不锁存时输出对于输入是透明的。



### latch的危害

 对毛刺敏感，不能异步复位，所以上电以后处于不确定的状态；

​    Latch会使静态时序分析变得非常复杂；

​    在PLD芯片中，基本的单元是由查找表和触发器组成的，若生成锁存器反而需要更多的资源。



### 产生原因

1，case

2，if-------else if

3，always@（敏感信号表）

### 解决方法

1.`case`——————加default：

关于`defalut`的情况：

一是可以`default：data=1‘bx`；这个x表示未知，在综合时可以避免产生锁存器。在仿真时是红线表示。

二是  `default：data=0`；这样产生一个默认的情况。

2.`if`-----------------------一定要有else语句。

3.`always`---------如是说道：在赋值表达式右边参与赋值的信号都必须在always@（敏感电平列表）中列出。

如果在赋值表达式右端引用了敏感电平列表中没有列出的信号，那么在综合时，将会为该没有列出的信号隐含地产生一个透明锁存器。

4. 赋初值，**好用**

### 例子

![latch_1](C:\Users\YUSN\Desktop\notebook\img\latch_1.png)

#### 错误代码

```verilog
always @(*) begin
    if (cpu_overheated)
       shut_off_computer = 1;
end

always @(*) begin
    if (~arrived)
       keep_driving = ~gas_tank_empty;
end
```

#### 错误原因

当else语句不完整时，`verilog`综合出latch使得不完备的信号在位置情况下保持不变，由于此时输出处于不定态，所以会产生死锁

#### 正确代码

```verilog
// synthesis verilog_input_version verilog_2001
module top_module (
    input      cpu_overheated,
    output reg shut_off_computer,
    input      arrived,
    input      gas_tank_empty,
    output reg keep_driving  ); //

    always @(*) begin
        if (cpu_overheated)
           shut_off_computer = 1;
        else 
           shut_off_computer = 0;
    end

    always @(*) begin
        if (~arrived)
           keep_driving = ~gas_tank_empty;
        else 
           keep_driving = 0;
    end

endmodule
```





