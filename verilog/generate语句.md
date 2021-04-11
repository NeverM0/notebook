## generate语句

genvar 循环变量名；

generate

  // generate循环语句，或generate 条件语句，或generate 分支语句

  // 或嵌套的generate语句

endgenerate

#### generate的结构类型

我们常用generate语句做三件事情。一个是用来构造循环结构，用来多次**实例化**某个模块。一个是构造条件generate结构，用来在多个块之间最多选择一个代码块，条件generate结构包含if--generate结构和case--generate形式。还有一个是用来断言。

在Verilog中，generate在建模（elaboration）阶段实施，出现预处理之后，正式模拟仿真之前。因此。generate结构中的所有表达式都必须是常量表达式，并在建模（elaboration）时确定。例如，generate结构可能受参数值的影响，但不受动态变量的影响。

Verilog中的generate块创建了新的作用域和新的层次结构，就像实例化模块一样。因此在尝试对generate块中的信号进行引用时，很容易因此混乱，因此请记住这一点。

**note:**genratekai中全是block,不能对generate块中的信号进行引用

```verilog
module top_module( 
    input [399:0] a, b,
    input cin,
    output cout,
    output [399:0] sum );

	genvar i;
    wire [99:0] cbit;
    bcd_fadd u_bcd_fadd(
        .a(a[3 : 0]),
        .b(b[3 : 0]),
        .cin(cin),
        .cout(cbit[0]),
        .sum(sum[3:0])
    );	
    generate 
        for(i=1;i<100;i++)
        begin:block1
            bcd_fadd u_bcd_fadd(
                .a(a[4*i+3 : 4*i]),
                .b(b[4*i+3 : 4*i]),
                .cin(cbit[i-1]),
                .cout(cbit[i]),
                .sum(sum[4*i+3 : 4*i]));	
        end
    endgenerate
    assign cout = cbit[99];
endmodule
```

注意在altera的环境下generate块中的块名不可缺少