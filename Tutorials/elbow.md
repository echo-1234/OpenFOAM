> This tutorial contains
> - converting gambit mesh to openfoam mesh 

Source: 
Source2: https://mp.weixin.qq.com/s/62S8K2zzwzZ7PoUbKWvbVw

# Preprocessing
` $FOAM_TUTORIALS/incompressible/icoFoam/elbow`\
> The ‘$’ sign allows the tutorial to be extracted from the installation directory
of OpenFOAM® under the current system environment.

## converting mesh
convert _Gambit_ mesh file to openfoam compatible.\
`fluentMeshToFoam elbow.msh -scale 1.0`\
> all the mesh in OpenFoam are in SI unit, therefore during conversion scaling needs to be performed with `-scale` flag, followed by scaling factor\
`fluent3DMeshToFaom` is used for converting mesh with __internal boundaries__.

# run
`icoFoam`

# Postprocessing
Create a line of plot using _Plot Over Line_ in paraview



