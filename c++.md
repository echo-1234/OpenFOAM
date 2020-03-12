# Introduction
## language
- Machine language (binary)
- Assembly language

## object oriented 
> C++: C with classes\
C++ can compile and run almost all C codes\
C is object-oriented language

C++ allows for self define classes. 
用一个最简单的例子表示面向对象和面向过程的区别：\
如果我们要求解矩阵A，面向过程是这样的代码：
objects are instances of a class

`solve(A);//矩阵A是一个参数`\
面向对象是这样的代码：

`A.solve();//矩阵A是一个对象`\

## characteristics
inheritance, incapsulation, polymorphism\
reduce the code redundance

### inheritance
> class definition
```
//- fvScalarMatrix的函数定义
fvScalarMatrix::solve()
{
    int a = 1;
    int b = 1;
    int c = 1;
    int d = 1;
    int e = a + b + c + d;

    //...
}
```
Inheritance
```
//- 大总管
fvMatrix::calculateE()
{
    int a = 1;
    int b = 1;
    int c = 1;
    int d = 1;
    int e = a + b + c + d;
}

//- fvScalarMatrix是fvMatrix类型的继承类
fvScalarMatrix::solve()
{
    calculateE()

    //...
}
```
### multimorphosis
conventional style with if sentences
```
if (turbulenceType == "RANS")
    RANS.solve();
if (turbulenceType == "LES")
    LES.solve();
if (turbulenceType == "PANS")
    PANS.solve();
if (turbulenceType == "DES")
    DES.solve();
else
    DNS.solve();
```
with auto pointers
```
autoPtr<turbulenceModel> turPtr(turPtr::New(turbulenceType));

turPtr->solve();
```

### Encapsulation


# Command

`cout << "hello CFD" << endl;`
> `cout` print to screening, content in quotation mark
> `endl` end line and start a new line. 

## Macro expansion
syntax `$` in front od a keyword for expansion.
```
a 10;
b $a; // the value of b is also 10
```
__Scoping__
syntax `.`, variables can be accessed within different levels of sub-directories
```
subdict
{
 a 10;
 }
 b $subdict.a;
```
- `..` up one lwl; `...`up two lvl; `:` to top level; 
- for multiple levels of macro substitution, each specified with the `$` dollar syntax, 
  `{}` brackets are required to protect the expansion, see below.
  
  ```
  a 10;
  b a; //the value of b is a
  c ${${b}}; // returns 10, since $b returns "a", and $a returns 10
  subdict
    {
        b $..a; // double-dot takes scope up 1 level, then "a" is available
        subsubdict
        {
            c $:a; // colon takes scope to top level, then "a" is available
        }
    }
  
  ```
## Including files
InitialConditions is a file containng 
```
pressure 1e+05;
```
To read the data contained in the file, use `#`\
`#include`学名叫预处理编译指令。目的在于简化代码.
```
#include "initialConditions"
internalField uniform $pressure;
boundaryField
{
 patch1
 {
   type fixedValue;
   value $internalField;
   }
}
```

`#include "<path>/<fileName>"`\
`#includeIfPresent "<path>/<fileName>"` read the file if it exist\
`#includeEtc "<path>/<fileName>"` read from <path> relative to _$FOAM_ETC_ directory\
`#includeFunc <fileName>` searched from the case system directory, followed by the $FOAM_ETC directory;\
`#remove <keywordEntry>` 


