
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
To read the data contained in the file, use `#`
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
