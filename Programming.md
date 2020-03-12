

OpenFOAM程序最基本的架构。每个程序会通过单独一个文件夹来包含（如myFirstFoam文件夹），在这个文件夹下，存在一个Make文件夹，Make中包含files和options文件。同时在myFirstFoam文件夹中，需要包含程序文件（如myFirstFoam.C）

- 目录文件夹下的.C文件是OpenFOAM/C++的主程序（注意大写）；

- Make文件夹是OpenFOAM特有的，里面主要包含两个文件files和options；

- files用来指定OpenFOAM顺序进行编译的文件名称以及路径；

- options用来指定OpenFOAM需要调用的外挂库的路径以及名称；

# Compile
## edit _Make/file_

```
myFirstFoam.C

EXE = $(FOAM_USER_APPBIN)/myFirstFoam
```
> - specify which file to compile
> - instruction on whether compile to executable (`EXE =`) or libraries (`LIB =`)
> - `EXE` means executable, followed by the file name

## `wmake`
   
