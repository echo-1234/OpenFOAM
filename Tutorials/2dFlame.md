> This tutorial contains information
> - Setting up reacting mixture and reactions
> - variable time step



`cp -r $FOAM_TUTORIALS/combustion/reactingFoam/laminar/counterFlowFlame2D .`

`reactingFoam`

A cube(2D), the left supply with methane, the right supply with air, the front and back are the outlets. laminar reaction. Initiation  with Nitrogen in the volume

# Procedure of the directories
## Mesh
1. setup the mesh with boundary specifications in _system\blockMeshDict_  
2. `blockMesh`

## setup properties in _constant_
   1. _thermophysicalProperties_
   ```
   thermoType
    {
    type hePsiThermo;
    mixture reactingMixture;
    transport sutherland;
    thermo janaf; 
    energy sensibleEnthalpy;
    equationOfState perfectGas;
    specie specie;
    }
    inertSpecie N2;
    chemistryReader foamChemistryReader;
    foamChemistryFile "$FOAM_CASE/constant/reactions";
    foamChemistryThermoFile "$FOAM_CASE/constant/thermo.compressibleGas";
   ```
     
   > - `thermo janaf;`  the heat capacities are calculated using “janaf polynomials”.
   > - `inertSpecie N2;` inert = 1- all other species
   > - The reactions are defined in `foamChemistryFile`
   > - The coefficients of each species are defined in the `foamChemistryThermoFile`, which reads the file _thermos.compressibleGas_ 
     
 2. _reactions_: 
   ```
   species
   (
       O2
       H2O
       CH4
       CO2
       N2
   );

   reactions
   {
     methaneReaction
     {
          type     irreversibleArrheniusReaction;
          reaction "CH4 + 2O2 = CO2 + 2H2O";
          A        5.2e16;
          beta     0;
          Ta       14906;
      }
  }
  ```
3. _chemistryProperties_: specifying chemistry solvers
4. _combustionProperties_: define combustion model (laminar in this example)

## setup the boundary and initial conditions in _0_ respective files
  1. all the species specified with no dimension (fraction), mass fraction (according to O2 and N2 file)
   - Ydefault (this might be the default file for defining species fraction)
     > If the file for a species does not exist in the 0 directory, the values from Ydefault will be used for that species.
   - O2 0.23 fixedValue for air inlet, 1.0 for outlet, internal 0.0
   - N2 0.77 for air inlet, internal 1.0
   - CH4 1.0 fixedValue for fuel inlet, internal 0.0
   - CO2, H2O included for product, initialize with all 0 
 2. alphat [kg/ms]
     > alpha, thermal diffusivity of enthalpy, same unit as dynamic viscosity
 3. T
 4. U
   ```
     outlet
    {
        type            pressureInletOutletVelocity;
        value           $internalField;
    }

   ```
 5. p
   ```
    outlet
   {
       type            totalPressure;
       p0              $internalField;
       U               U;
       phi             phi;
       rho             none;
       psi             none;
       gamma           1;
       value           $internalField;
   }
 ```
## _system_
   - _controlDict_
     ```
     runTimeModifiable true;
     adjustTimeStep  yes;
     maxCo           0.4;
     ```
     > the solver automatically ignores `deltaT`, and calculates the `deltaT` based on the maximum Courant number `maxCo` defined for it.
     ```
     writeControl adjustableRunTime;
     writeInterval 10; //10s interval
     ```

# Adding new species
1. edit _constant/thermos.compressibleGas_
2. edit _constant/reactions_
3. add initialization file in _0_
