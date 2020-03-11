
`cp -r $FOAM_TUTORIALS/combustion/reactingFoam/laminar/counterFlowFlame2D .`

`reactingFoam`

The directories\

_system_\
_blockMeshDict  controlDict  fvSchemes  fvSolution_
_constant_\
_chemistryProperties   g          thermo.compressibleGas    turbulenceProperties
combustionProperties  reactions  thermophysicalProperties_\

# Procedure of the directories

1. setup the mesh with boundary specifications in _system\blockMeshDict_  
2. `blockMesh`

3. setup properties in _constant_
     - _thermophysicalProperties_
     ```
     // * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *//
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
    // * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *//
     ```
     > `thermo janaf;`  the heat capacities are calculated using “janaf polynomials”.
     > `inertSpecie N2;` inert = 1- all other species
     > The reactions are defined in `foamChemistryFile`
     > The coefficients of each species are defined in the `foamChemistryThermoFile`, which reads the file _thermos.compressibleGas_ 
     - _reactions_: 
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
    
   -
 4. setup the boundary and initial conditions in _0_ respective files
   - all the species specified with no dimension (fraction), mass fraction (according to O2 and N2 file)
   - Ydefault (this might be the default file for defining species fraction)
     > If the file for a species does not exist in the 0 directory, the values from Ydefault will be used for that species.
   - O2 0.23 fixedValue for air inlet, 1.0 for outlet, internal 0.0
   - N2 0.77 for air inlet, internal 1.0
   - CH4 1.0 fixedValue for fuel inlet, internal 0.0
   - CO2, H2O included for product, initialize with all 0 
   - alphat [kg/ms]
     > alpha, thermal diffusivity of enthalpy, same unit as dynamic viscosity
   - T
   - U
   ```
     outlet
    {
        type            pressureInletOutletVelocity;
        value           $internalField;
    }

   ```
   - p
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
