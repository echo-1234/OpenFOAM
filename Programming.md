

OpenFOAM程序最基本的架构。每个程序会通过单独一个文件夹来包含（如myFirstFoam文件夹），在这个文件夹下，存在一个Make文件夹，Make中包含files和options文件。同时在myFirstFoam文件夹中，需要包含程序文件（如myFirstFoam.C）

- 目录文件夹下的.C文件是OpenFOAM/C++的主程序（注意大写）；

- Make文件夹是OpenFOAM特有的，里面主要包含两个文件files和options；

- files用来指定OpenFOAM顺序进行编译的文件名称以及路径；

- options用来指定OpenFOAM需要调用的外挂库的路径以及名称；

# Compile
## edit _Make/files_

```
myFirstFoam.C

EXE = $(FOAM_USER_APPBIN)/myFirstFoam
```
> - specify which file to compile
> - instruction on whether compile to executable (`EXE =`) or libraries (`LIB =`)
> - `EXE` means executable, followed by the file name
## edit _Make/options_
to include the __path__ and __name__ for the external libraries
```
EXE_INC = \
    -I$(LIB_SRC)/finiteVolume/lnInclude \
    -I$(LIB_SRC)/meshTools/lnInclude
    
```
> - `\` is used for changing lines in OpenFOME, otherwise error message will display _Make/linux64GccDPInt32Opt/options:54: *** missing separator_
> - `EXE_INC =` is followed by the path of the header file included in the codes
> - `$(LIB_SRC)` is an environmental varable, equivalent to `$(FOAM_SRC)` in OpenFOAM


## `wmake` compile under Openfoam structure
`wclean` is used to clean up the environment.

# Run
## `./<executable>` to run

# Basic Commands
- `volScalarField` scalar
- `volVectorField`  vector field
- `fvScalarMatrix` example linear system of turbulent kinetic energy ??
- `autoPr` OpenFoam smart pointer
- `processor` processor type; parallel running process type
___for_ loop__
```
int results = 0;

for (int k = 1, k < 11, k++)
{
    results = results + k;    
}
```
___while_ loop_
> used for velocity and pressure iterative correction 
```
while (nOuterCorrectors < 3)
{
    pCorr();
}
```

___if___

__logic__
`&&` and; `||` or

   
