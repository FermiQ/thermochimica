## Overview
The `Thermochimica.f90` file is the main thermochemical solver for the Thermochimica library. It is responsible for computing the quantities of species and phases at thermodynamic equilibrium and various other thermodynamic quantities. It takes input variables such as temperature, pressure, elemental composition, and a thermodynamic database file to perform these calculations.

## Key Components
- `ThermochimicaSetup`: Initializes the calculation by checking inputs, initializing modules, checking system consistency, computing thermodynamic data from the database, and verifying this data.
- `ThermochimicaSolver`: Performs the core equilibrium calculation. It can load reinitialization data, uses a Leveling algorithm to estimate the equilibrium phase assemblage, optionally applies fuzzy stoichiometry, and then uses a Gibbs Energy Minimization (GEM) solver to find the final equilibrium state. It also handles retries and post-processing calculations like heat capacity.
- `Thermochimica`: The main entry point subroutine that calls `ThermochimicaSetup` and then `ThermochimicaSolver`.

## Important Variables/Constants
- `INFOThermo`: An integer output scalar that indicates the success or failure of the computation. Error codes are described in `ThermoDebug.f90`.

## Usage Examples
Thermochimica is typically used in one of three ways:
1. By writing and compiling Fortran driver files that call the `Thermochimica` subroutine (after parsing a data file with `ParseCSDataFile`).
2. Using input scripts processed by `InputScriptMode`.
3. Through Graphical User Interfaces (GUIs).

The primary Fortran usage involves:
1. Calling `ParseCSDataFile` to load thermodynamic data.
2. Calling the `Thermochimica` subroutine with inputs like temperature, pressure, elemental composition, and units.
3. Checking the `INFOThermo` variable for results.
4. Calling `ResetThermoAll` to reset the parser and Thermochimica.

Input variables typically include:
- `dTemperature`: Temperature (double real scalar).
- `dPressure`: Absolute hydrostatic pressure (double real scalar).
- `dElementMass`: Mass of each chemical element (double real vector).
- `cThermoInputUnits`: Units of input variables (character vector).
- `cThermoFileName`: Path and name of the thermodynamic data-file.

Output includes stable phases, mole fractions, chemical potentials, etc.

## Dependencies and Interactions
The `Thermochimica.f90` subroutines primarily use `ModuleThermoIO` (for input/output variables and flags) and `ModuleThermo` (for core thermodynamic data and solver variables).

It calls and interacts with several other key Fortran files/subroutines:
- `CheckThermoInput.f90`: Validates input variables.
- `InitThermo.f90`: Initializes Thermochimica constants and tolerances.
- `CheckSystem.f90`: Checks consistency between the data-file and the current system.
- `CompThermoData.f90`: Computes thermodynamic data from the database.
- `CheckThermoData.f90`: Validates the computed thermodynamic data.
- `LevelingSolver.f90`: Estimates initial equilibrium phase assemblage.
- `RandomizeStoichiometry.f90`: Applies fuzz to stoichiometries if requested.
- `GEMSolver.f90`: The main Gibbs Energy Minimization solver.
- `ResetStoichiometry.f90`: Resets stoichiometry after fuzzy application.
- `RetryCalculationFirstPhase.f90`: Handles retrying calculations.
- `PostProcessThermo.f90`: Performs post-processing of results.
- `HeatCapacity.f90`: Calculates heat capacity.
- `LoadReinitData.f90`: Loads data for reinitializing a calculation.
- `ParseCSDataFile.f90` (called externally before `Thermochimica`): Parses the thermodynamic database.
- `ResetThermoAll.f90` (called externally after `Thermochimica`): Resets the library state.
