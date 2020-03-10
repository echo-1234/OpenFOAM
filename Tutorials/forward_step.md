> This tutorial contains
> - _thermophysicalProperties_ file setup




`rhoPimpleFoam` solver is a transient solver. It solves trans-sonic/supersonic, turbulent flow of a
compressible gas/fluid.\

Copy tutorial files:
`cp -r $FOAM_TUTORIALS/compressible/rhoPimpleFoam/laminar/forwardStep .`

## Initialization directory _0_
## _Constant_ directory 
```
// * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *//
thermoType
{
  type hePsiThermo;
  mixture pureMixture;
  transport const;
  thermo hConst;
  equationOfState perfectGas;
  specie specie;
  energy sensibleInternalEnergy;
}
  // Note: these are the properties for a “normalized” inviscid gas
  // for which the speed of sound is 1 m/s at a temperature of 1K
  // and gamma = 7/5
mixture
{
  specie
  {
  molWeight 11640.3;
  }
  thermodynamics
  {
  Cp 2.5;
  Hf 0; // heat of fusion
  }
  transport
  {
  mu 0; // dynamic viscosity
  Pr 1; 
  }
}
// * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *//
```
> __`thermoType`__\
> - `type`
> - `mixture` eg. pure mixture; homogeneous mixture; reacting mixture
> - `transport`  transport model
> - `thermo` method for calculating heat capacity
> - `equationOfState` used for compressibility of gas, `perfectGas` for ideal gas
> - `energy` which type of energy equation it should solve, enthalpy or internal energy.
