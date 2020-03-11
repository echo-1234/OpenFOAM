# OpenFOAM

This repository contain notes and everything while learning openfoam2.4


`$WM_PROJECT_DR` this direct to openfoam main source folder\
`$FOAM_TUTORIALS` direct to the tutorials folder under openfoam main directory\
> eg. `cp -r $FOAM_TUTORIALS/incomplressible/icoFoam/elbow .` copying the folder to the current directory.


# Classes in OpenFoam

1. Utilities\
   used for data manipulation during pre- and post-processing\
   eg. `mapFields`
2. Solvers\
   used for sovling the equations\
   eg. `icoFoam`, `pisoFoam`
   
3. Dictionary\
   specify the values and for use during the run etc.\
   for user to make selections etc, the fore identity of user interface\
   eg. _controlDict_
   a utility can be accounted for a disctionary for setting up parameters, eg. _mapFieldsDict_\
   
lists are specified with keywords under classes

# File structures of OpenFoam (directory tree)
![alt text](https://cdn.cfd.direct/docs/user-guide-v4/img/user281x.png)
- _time_ directories: _0_ containing the fields initial values, one file for one variable
- _system_: for setting parameters associated with the solution procedure itself. (first three must include)
  - file _controlDict_: run control parameters are set including start/end time, time step and parameters for data output (I/O control)
  - file _fvScheme_: discretisation schemes used in the solution may be selected at run-time
  - file _fvSolution_: the equation solvers, tolerances and other algorithm controls are set for the run
- _constant_: 
  - sudirectory _polyMesh_: a full description of the case mesh (file _blockMeshDict_)
  - file _trasnportProperties_: specifying physical properties for the application concerned
  - file _turbulentPropertied_: turbulence model selection
  
  ## Initial condition file (_0_)
  - `dimensions ` specify units [  mass  length  time  temperature quantity [mol]  current  luminous intensity ]
  - `internalField`: _uniform_ or _nonuninform_
  - `boundaryField`: \
     _zeroGradient_ (Neumann boundary condition); _fixedValue_ (Dirichlet boundary); _empty_: for sides vertical to the direction not considered (eg, 2D simulation);\
     The name of all the boundaries corresponds to those in _blockMeshDict_ or _constant/polyMesh/boundary_ after mesh is generated.
   
  ## constant directory
  ## system directory
  
  # Solver setup
  ## reacting flow
  1. _constant/thermoPhysicalProperties_ 
  2. _constant/reactions_ specify species and reactions
  3. _constant/thermo.compressibleGas_ specify coefficients of each species, the number of species is listed in this file
    > - In the _thermodynamics_ sub-dictionary the janaf polynomial model coefficients for calculating the heat capacity can be found 
    > - in _transport_ the sutherland model coefficients for viscosity are stored.
    > - _$FOAM_ETC/thermoData/thermoData_ store the coeffecients to be used for heat capacity with the corrected file. 
  1. initialization _0_ with all the species and boundary conditions
  
  # porous media
  $FOAM_ETC/caseDicts/general/fvOptions/porosity/porousZone

