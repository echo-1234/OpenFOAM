Based on openfoam 3.0.1 packge in NUS HPC environment

> __This tutorial cover the following__
> - Simple mesh creation and boundary condition initiation
> - setup the control (calculate delta_t from _Courant number_ limit), the property and solver selection
> - run the process in foreground, or backgroud with log file, __`icoFoam` solver__
> - using paraView for postprocessing
> - mapping coarser mesh results into finer mesh and compare
> - mesh grading (including mesh size estimation and deltaT from Courant) and setup 
> - High Reynolds number problem with __standard k-e__ under __`pisoFoam` solver__ (with estimation for k and e boudnary conditions)
> - changing the geometry and inconsistent mapping


# Pre-processing

## Create mesh
`blockMesh`
> generates meshes from a description specified in an input dictionary, 
> `blockMeshDict` located in the `system` (or `constant/polyMesh`) directory for a given case.

`FoamFile` sub-dictionary\
`blockMeshDic`
- The file first specifies coordinates of the block vertices (vertice numeber starting from 0); 
- it then defines the blocks (here, only 1) from the vertex labels and the number of cells within it.\
  ` simpleGrading (1 2 3)` cell expansion ratios (between the first to the last cell) in x1, x2, x3 directions
- The edges and patches (surfaces) are also represented by the numberd vertice.


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
deltaT value need to meet the criterion that the _Courant number < 1_\
![alt text](https://cdn.cfd.direct/docs/user-guide-v4/img/user23x.png)

## Discretization and linear-solver settings
`\system\fvSchemes` dictionary: user specifies the choice of finite volume discretisation schemes 
`\system\fvSolution` dictionary: specify the linear equation solver and tolerance and other algorithm controls.
> `PISO` sub-sictionary: `pRefCell` and `pRefValue` ( Changing either of these values will change the absolute pressure field, but not the relative pressures or velocity field.)

# ~~Viewing the mesh~~
__This method does not work for HPC, see post-processing below__
__ParaView__\
- within the case directory\
  ```
  paraFoam & \\'&' run the executable in the background as it is convenient to keep ParaView while runnig
  ```
- launch from other directory\
```
paraFoam -case $FORM_RUN/cavity &  //change the path accordingly
```

Before clicking the _Apply_ button to load the geometry into _ParaView_, the user needs to select some geometry from the _Mesh Parts_ panel

# Running an application
- case directory
  `icoFoam`
- command prompt with optional -case and case directory
  `icoFoam -case $FOAM_RUN/cavity`
> The progress of the job is written to the terminal window. It tells the user the current time, maximum Courant number, initial and final residuals for all fields.

# Post-processing
- convert to VTK\
  `foamtoVTK` 
  > VTK is paraview format
  
  `paraview &` to run paraview in background

- run directly\
  Alternatively,\
  Create an empty text file in main case directory, <name>.foam (for example: cavity.foam)\
  Then `paraview cavity.foam &`
 
 # Increasing mesh resolusion
 The results from coarser mesh case will map to the finer case as initial condition, and the results can be compared.
 1. creating new case
    ```
    cd .. \\ the directory containing cavity and other cases
    
    mkdir cavity_fine
    cp -r cavity/constant cavity_fine
    cp -r cavity/system cavity_fine
    
    cd cavity_fine
    ```
 2. creat finer mesh
    open the `blockMeshDict` file
    `nano <directory>\blockMeshDict`
    in the `block` list,  following hex is first the list of vertices in the block, then a list (or vector) of numbers of cells in each direction. (change the (20, 20, 1) into (40, 40, 1) will increase the mesh in X and Y direction by double
    ```  
    blocks
    (
        hex (0 1 2 3 4 5 6 7) (20 20 1) simpleGrading (1 1 1)
    );
    
    ```
    `blockMesh`
    
 3. map coarse mesh results onto fine mesh
   `mapFields` 'consistent' indicate the geometry and boundary types are same for both the source and target fields.
   The field data that `mapFields` maps is read from the time directory specified by `startFrom` and `startTime` in the _controlDict_ of the __target case__. 
   The final result in the coarse case is store in _0.5_ directory of _cavity_, the `startTime` should be 0.5 in the _controlDict_. 
   ```
   nano system\controlDict \\edit the file and save
   mapFields ../cavity -consistent
   ```
 4. control adjustments `controlDict`
   - adjust time step (to maintain Courant number less than 1)
   - change writeControl from `timeStep` to `runTime`
   ```
   writeControl   runTime;
   writeInterval  0.1;
   ```
   - change `endTime` to 0.7s for new convergence
   
   5. run at background and _log_ file the output.
   from _'cavity_fine'_ case directory
   ```
   icoFoam > log &
   cat log
   ```   
   6. vector plot
      can open multiple cases simultaneously in paraview 

# Mesh Grading
The example used tutorial file in `$FOAM_TUTORIALS/incompressible/icoFoam/cavityGrade`\
1. mesh
In steady of one block, the graded mesh requires dividing the region into more blocks. This means more vertices etc.\
Each block now has 10 cells in the x and y directions and the ratio between largest and smallest cells is 2.\
```
    blocks
    (
        hex (0 1 4 3 9 10 13 12) (10 10 1) simpleGrading (2 2 1) \\ cell expansion 2 towards the ceter
        hex (1 2 5 4 10 11 14 13) (10 10 1) simpleGrading (0.5 2 1) \\ cell expansion ration 0.5 away from the center
        hex (3 4 7 6 12 13 16 15) (10 10 1) simpleGrading (2 0.5 1)
        hex (4 5 8 7 13 14 17 16) (10 10 1) simpleGrading (0.5 0.5 1)
    );
```
2.time and time step

(Courand number limit) through estimating the smallest cell size\
( this example, l=0.05m, R=2, n=10, substitute in we get delta_xs = 3.45mm -> delta_t<3.45 ms)
> When a nonuniform mesh grading is used, blockMesh calculates the cell sizes using a geometric progression. Along a length _l_, if _n_ cells are requested with a ratio of _R_ between the last and first cells, the size of the smallest cell, _δxs_, is given by:\
> ![alt text](https://cdn.cfd.direct/docs/user-guide-v4/img/user70x.png)\
> where r is the ratio between one cell size and the next which is given by:\
> ![alt text](https://cdn.cfd.direct/docs/user-guide-v4/img/user72x.png)\
> ![alt text](https://cdn.cfd.direct/docs/user-guide-v4/img/user73x.png)

# Increasing the Reynolds number
increasing the _Re_ from 10 to 100 and find the effect on convergence (with different mesh)
```
cd run
foamCloneCase -latestTime cavity cavityHighRe 
//foamClone copy the reletive directory in one step
// -lastestTime can copy the latest time directory (only the latedt is copyied, and will be used as initial)
cd cavityHighRe
```
1. pre-processing
- edit `trasnportProperties` dictionary in constant\
  incease kinematic viscosity by a factor of 10
- edit `controlDict`
  ```
  startFrom latestTime
  endTime 2
  ```
2. run
```
nohup nice -n 19 icoFoam log &
cat log
```
> - `nohup` enables a command to keep running after the user who issues the command has logged out;
> - `nice` changes the priority of the job in the kernel’s scheduler; a niceness of -20 is the highest priority and 19 is the lowest priority.

log file _No Iteration 0_ indicates that the solution has stopped

# High Reynolds number flow
Use __standard k-e model with wall function__ to solve lid-drive with Re=10^4
OpenFOAM solver _pisoFoam_
## 1. Pre-processing
### files and mesh
- `cp -r $FOAM_TUTORIALS/incompressible/pisoFoam/ras/cavity cavityRAS`
- mesh\
  > Mesh grading towards the wall is not necessary when using the standard k − ε model with wall functions since the flow in the near wall cell is modelled, rather than having to be resolved.\
### initial condition
- __wall function__
The choice of wall function models are specified through the turbulent viscosity field, _νt_ in the _0/nut_ file:
```
        movingWall
        {
            type            nutkWallFunction;
            value           uniform 0;
        }

```
> `nutkWallFunction` specifies standard wall functions
> `nutRoughWallFunction` specifies rough wall functions

- __standard k-e initial condition (_0/epsilon_ and _0/k_)__\
  `epsilonWallFunction` for _epsilon_\
  `kqRwallFunction` for _k_, and this is generic boundary condition that are of a turbulent kinetic energy type (eg. _k_, _q_ or Reynolds Stress _R_)
  > estimation from fluctuating componet of velocity __U'__, C_miu is a constant of k-e equals 0.09\
  > ![alt text](https://cdn.cfd.direct/docs/user-guide-v4/img/user104x.png)\
  > ![alt text](https://cdn.cfd.direct/docs/user-guide-v4/img/user104x.png)\
  > for catesian ![alt text](https://cdn.cfd.direct/docs/user-guide-v4/img/user120x.png)\
 In this example the fluctuation component is assumed to  be 0.05*velocity, => k = 3.75e-3 m2s-2; e = 7.54e-3 m2s-3. 
 
 ### simulation type (eg. RAS or LES)
 `simulationType`keyword can be found in _constant/turbulenceProperties_ to specify which to use; choices include `laminar`, `RAS`and `LES`
 ``` 
     simulationType  RAS;
    
     RAS
    {
        RASModel        kEpsilon; // standard k-e model
  
        turbulence      on;
  
        printCoeffs     on;
    }
 ```
 > make sure `turbulence` is on\
 > `printCoeffs` make teh default values printed in standard output into sub-directories appended with _Coeffs_
 
 ### properties and run control
 laminar kinetic viscosity\
 _controlDict_ start and end and time interval
 
 ## run 
 `pisoFoam`\
 since after ~100 time steps, the Courant number stays below 0.2, it is sensible to increase `deltaT` to 0.02s so that the Courant number is closer to 1 (faster calculation) and change the start and stop time to cotinue running (`endTime 20.0`)
 > View the results at consecutive time steps as the solution progresses to see if the solution converges to a steady-state or perhaps reaches some periodically oscillating state. In the latter case, convergence may never occur but this does not mean the results are inaccurate.
 
 # Change the case geometry
 However the `mapFields` utility can map fields that are inconsistent, either in terms of geometry or boundary types or both.\
 `cp -r $FOAM_TUTORIALS/incompressible/icoFoam/cavityClipped .`\
 `blockMesh`
 
 ## __inconsistent mapping__
> In an inconsistent mapping, there is no guarantee that all the field data can be mapped from the source case. The remaining data must come from field files in the target case itself. Therefore field data must exist in the time directory of the target case before mapping takes place. In the cavityClipped case the mapping is set to occur at time 0.5 s, since the startTime is set to 0.5 s in the controlDict. Therefore the user needs to copy initial field data to that directory.\
1.  `cp -r 0 0.5`\
2. Edit the _mapFieldsDict_ under _system_ directory
```
patchMap        ( lid movingWall ); // (<target source>)

cuttingPatches  ( );//  empty means no mapping. 
```
  > - The `patchMap` list contains a mapping of patches from the source fields to the target fields. It is used if the user wishes a patch in the target field to inherit values from a corresponding patch in the source field.
  > - The cuttingPatches list contains names of target patches whose values are to be mapped from the source internal field through which the target patch cuts.\
  In this case, the fixedWalls patch is a noSlip condition so the internal values cannot be interpolated to the patch. Therefore the cuttingPatches list can simply be empty; If the user does wish to interpolate internal values from the source case to the fixedWalls patch in the target case, a fixedValue boundary condition needs to be specified on the patch, whose value can then be overridden during the mapping process; the fixedWalls patch then needs to be included in the cuttingPatches list.
3. map
`mapFields ../cavity`

## run
   `icoFoam`
