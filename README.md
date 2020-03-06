# OpenFOAM

This repository contain notes and everything while learning openfoam2.4


`$WM_PROJECT_DR` this direct to openfoam main source folder\
`$FOAM_TUTORIALS` direct to the tutorials folder under openfoam main directory\
> eg. `cp -r $FOAM_TUTORIALS/incomplressible/icoFoam/elbow .` copying the folder to the current directory.


Classes in OpenFoam

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
