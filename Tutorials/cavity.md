## Create mesh

`blockMesh`

> generates meshes from a description specified in an input dictionary, 
> `blockMeshDict` located in the `system` (or `constant/polyMesh`) directory for a given case.

`FoamFile` sub-dictionary\
`blockMeshDic`
> The file first specifies coordinates of the block vertices; 
> it then defines the blocks (here, only 1) from the vertex labels and the number of cells within it; 
> and finally, it defines the boundary patches.

## Initialization (directory `0`)
The case is set up to start at time t = 0 s, so the initial field data is stored in a `0` sub-directory of the `cavity` directory\
The `0` sub-directory contains 2 files, `p` and `U`, one for each of the pressure (`p`) and velocity (`U`) fields whose initial values and boundary conditions must be set. 

 ### Pressure `p`
 3 entries
 
 `dimensions [0 2 -2 0 0 0 0]; `\
 |mass |length |time |temperature| quantity [mol] |current |luminous| intensity|
 
 `internalField uniform 0;`\
 _uniform_ define by single value `internalField uniform <entry>;`
 _nonuniform_ all the values of the field must be specified `internalField nonuniform <List>;`
 
 `boundaryField`\
 this is a dictionary containing all the boundary patches
 ```
 boundaryField
  {
      movingWall
      {
          type            zeroGradient;
      }
  
      fixedWalls
      {
          type            zeroGradient;
      }
  
     frontAndBack
      {
          type            empty;
      }
  }
 ```
 
 - for this case, the wall splits into 2 patches (1)_fixedWalls_; (2)_movingWalls_ 
   given _zeroGradient_ for _p_: "the normal gradient of the pressure is zero"
 - (3)_frontAndBack_ represent front and back plane, set as empty

### Velocity `U`
 `internalField uniform (0 0 0);` three vector components;
 ```
 boundaryField
 {
  movingWall
  {
    type fixedValue;
    value uniform (1 0 0);
  }
  
  fixedWalls 
  {
    type fixedValue;
    value uniform (0 0 0);
  }
  
  frontAndBack
  {
    type empty;
  }
 }
 ```
 
 ## Pysical properties
 The physical properties for the case are stored in dictionaries whose names are given the suffix `…Properties`, located in the `Dictionaries` directory tree

_icoFoam_
kinematic viscosity (keyword `nu`) stored in `transportProperties` dictionary
```
nu    [0 2 -1 0 0 0 0] 0.01;
```

## Control
`controlDict` dictionary as case control files under `system` directory
start at t=0 and OpenFOAM read field data from dicrectory `0`
```
application  icoFoam;

startFrom startTime;
startTime 0;
stopAt    endTime;
endTime   0.5;
deltaT    0.005; //time step

writeControl    timeStep;
writeInterval   20;
purgeWrite      0;
writeFormat     ascii;
writePrecision  6;
writeCompression off;
timeFormat      general;
timePrecision   6;
runTimeModifiable true;
```
deltaT value need to meet the criterion that the _Courant number < 1_
![alt text](https://cdn.cfd.direct/docs/user-guide-v4/img/user23x.png)

## Discretization and linear-solver settings
`\system\fvSchemes` dictionary: user specifies the choice of finite volume discretisation schemes 
`\system\fvSolution` dictionary: specify the linear equation solver and tolerance and other algorithm controls.
> `PISO` sub-sictionary: `pRefCell` and `pRefValue` ( Changing either of these values will change the absolute pressure field, but not the relative pressures or velocity field.)
