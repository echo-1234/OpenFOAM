# Stady-state turbulent models

## _0_ directory
a list of files which should be available at 0 directory and need to be modified for each turbulence model
- laminar: no file
- kEpsilon (RAS): k and epsilon
- kOmega (RAS): k and omega
- LRR (RAS): k, epsilon and R 
>  ~A missing _R_ file can be created by OpenFOAM®. Open the _constant/turbulenceProperties_ file, set the `simulationType` to `RAS` and `RASModel` to `kEpsilon`. Run the command `simpleFoam –postProcess –func R` from terminal, it will create the R file in the 0 directory.~
- Smagorinsky (LES): nuSgs
- kEqn (LES): k and nuSgs – This model is called ‘oneEqEddy’ in V2.3.0
- SpalartAllmaras (LES): nuSgs and nuTilda

##_constant_
_turbulenceProperties_
For the laminar model, set `turbulence` and `printCoeffs` to `off`.

## _system_
For the LRR model, discretization model for the new variable R needs to be specified. It is done through the `fvSchemes` file,
