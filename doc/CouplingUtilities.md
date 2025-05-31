## Overview
The `CouplingUtilities.f90` file provides a collection of utility subroutines designed to facilitate the interaction with the Thermochimica library. These subroutines allow users to set input parameters, define calculation conditions, retrieve results, and manage other aspects of the simulation workflow. They are particularly useful for coupling Thermochimica with other codes or for scripting calculations.

## Key Components
This file contains numerous subroutines. Key functionalities include:
- **Setup and Configuration:**
    - Setting the thermodynamic database file name (`SetThermoFileName`).
    - Setting units for temperature, pressure, and mass (`SetUnitTemperature`, `SetUnitPressure`, `SetUnitMass`, `SetStandardUnits`, `SetModelicaUnits`, `SetUnits`).
    - Setting calculation temperature and pressure (`SetTemperaturePressure`).
    - Managing print output verbosity (`SetPrintResultsMode`).
    - Presetting and setting element masses (`PresetElementMass`, `SetElementMass`).
    - Controlling reinitialization behavior (`SetReinitRequested`, `setReinitData`).
    - Requesting heat capacity, enthalpy, and entropy calculations (`SetHeatCapacityEnthalpyEntropyRequested`).
    - Controlling fuzzy stoichiometry options (`SetFuzzyStoich`, `SetFuzzyMagnitude`).
    - Toggling Gibbs minimization checks (`SetGibbsMinCheck`).
    - Specialized initialization for ZIRCO and UO2PX databases (`SSInitiateZRHD`, `SSInitiateUO2PX`).
- **Retrieving Results and System Information:**
    - Getting the number of solution and condensed phases in the system (`GetNumberPhasesSystem`).
    - Getting the number of species in each solution phase (`GetNumberSpeciesSystem`).
    - Retrieving element mass (`GetElementMass`).
    - Checking the Thermochimica error/info status (`CheckINFOThermo`, `ResetINFOThermo`).
    - Calculating total moles of an element in solution phases (`SolPhaseParse` - noted as a 'quick hack').
    - Getting mole fraction of a species (`getMolFraction`).
    - Getting chemical potential of a species (`getChemicalPotential`).
    - Getting element potential (`getElementPotential`).
    - Retrieving reinitialization data sizes and values (`getReinitDataSizes`, `getReinitData`).
    - Getting moles of each phase (`GetMolesPhase`).
    - Calculating Gibbs energy from reinitialization data (`GibbsEnergyOfReinitData`).
    - Getting the phase assemblage (`GetAssemblage`).
    - Getting all element potentials (`GetAllElementPotential`).
    - Getting element fraction in the system (`GetElementFraction`).
    - Retrieving calculated heat capacity, enthalpy, and entropy (`GetHeatCapacityEnthalpyEntropy`).
- **String and Data Manipulation Utilities:**
    - Converting parts-per-million (ppm) to moles (`APpmInBToMolInVol`).
    - Tokenizing a string (`tokenize`).
    - Removing null characters from C strings (`chomp`, `chopnull`).
    - Matching a word in a dictionary (`matchdict`).

## Important Variables/Constants
This file primarily interacts with variables stored in `ModuleThermoIO` and `ModuleThermo`. There are no module-level constants defined directly within `CouplingUtilities.f90` itself, but the subroutines manipulate many important control flags and data arrays for the Thermochimica library (e.g., `cThermoFileName`, `dTemperature`, `dPressure`, `dElementMass`, `INFOThermo`, etc.).

## Usage Examples
These utility subroutines are typically called from a Fortran-based application or script that drives Thermochimica.

Example: Setting up a calculation and retrieving some results.
```fortran
USE CouplingUtilities ! Assuming an interface block or module is available
USE ModuleThermoIO    ! For direct access to some variables if needed

implicit none

real(8) :: temp, press, o_to_m_ratio, heat_cap, enthalpy, entropy
integer :: info_status, num_soln_phases, num_con_phases
character(len=256) :: db_file

db_file = 'path/to/your/thermo_data.dat'
call SetThermoFileName(db_file)
call SetStandardUnits() ! Use K, atm, moles

temp = 1273.15 ! Temperature in Kelvin
press = 1.0    ! Pressure in atm
call SetTemperaturePressure(temp, press)

! ... (Set element masses using SetElementMass or PresetElementMass) ...

call Thermochimica() ! Assuming this is the main calculation call

call CheckINFOThermo(info_status)
if (info_status /= 0) then
    print *, 'Thermochimica calculation failed with code: ', info_status
    stop
end if

call GetNumberPhasesSystem(num_soln_phases, num_con_phases)
print *, 'Number of solution phases: ', num_soln_phases
print *, 'Number of condensed phases: ', num_con_phases

call SetHeatCapacityEnthalpyEntropyRequested(1) ! Request these calculations
call ThermochimicaSolver() ! May need to call solver again or a specific post-processing step
call GetHeatCapacityEnthalpyEntropy(heat_cap, enthalpy, entropy)
print *, 'Heat Capacity: ', heat_cap
print *, 'Enthalpy: ', enthalpy
print *, 'Entropy: ', entropy

! ... (Call other Get... subroutines as needed) ...
```
**Note:** The exact sequence of calls, especially around re-running the solver for post-processing quantities like heat capacity, would depend on the main Thermochimica workflow.

## Dependencies and Interactions
- **Modules:**
    - `ModuleThermoIO`: Extensively used to access and modify input/output control variables, flags (e.g., `cThermoFileName`, `dTemperature`, `dPressure`, `dElementMass`, `INFOThermo`, `lReinitRequested`, `lHeatCapacityEntropyEnthalpy`).
    - `ModuleThermo`: Used to access core thermodynamic data and system properties calculated by Thermochimica (e.g., `nSolnPhasesSys`, `nConPhasesSys`, `nSpeciesPhase`, `dMolFraction`, `dChemicalPotential`, `dElementPotential`, `iAssemblage`, `dMolesPhase`).
    - `ModuleGEMSolver`: Used by `getReinitData` to retrieve `iterGlobal`.
    - `ModuleReinit`: Used by subroutines dealing with reinitialization data (`getReinitData`, `setReinitData`, `GibbsEnergyOfReinitData`).
- **External Subroutines:**
    - Some subroutines like `SSInitiateZRHD` and `SSInitiateUO2PX` call `SetThermoFileName` and `SetUnits` internally.
    - `GibbsEnergyOfReinitData` calls the `GibbsEnergy` subroutine (presumably from `src/postprocess/GibbsEnergy.f90`).
- **General Workflow:** These utilities are intended to be called by an external driver program that orchestrates calls to the main Thermochimica parsing and solving routines. They provide a higher-level interface for many common setup and data retrieval tasks.
