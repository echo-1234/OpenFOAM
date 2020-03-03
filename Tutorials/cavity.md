
`blockMesh`

> generates meshes from a description specified in an input dictionary, 
> `blockMeshDict` located in the `system` (or `constant/polyMesh`) directory for a given case.

`FoamFile` sub-dictionary\
`blockMeshDic`
> The file first specifies coordinates of the block vertices; 
> it then defines the blocks (here, only 1) from the vertex labels and the number of cells within it; 
> and finally, it defines the boundary patches.

## Initialization
The case is set up to start at time t = 0 s, so the initial field data is stored in a `0` sub-directory of the `cavity` directory\
The `0` sub-directory contains 2 files, `p` and `U`, one for each of the pressure (`p`) and velocity (`U`) fields whose initial values and boundary conditions must be set. 

