## Overview
The `FindSolnSpecies.f90` file provides a subroutine named `FindSolnSpecies` that calculates the total number of moles of a specified chemical element present within a given list of solution species (phases). This is useful for determining how much of an element is distributed among particular solution phases at equilibrium.

## Key Components
- `FindSolnSpecies(cCompName, iLCompName, iElem, dMolSum)`:
    - `cCompName` (input character string): A comma-separated string containing the names of the solution species (phases) to consider.
    - `iLCompName` (input integer): The length of the `cCompName` string. If 0 or less, the Fortran `len()` intrinsic is used. If positive, it's assumed to be the C-style string length (e.g., from `strlen`).
    - `iElem` (input integer): The atomic number (index) of the chemical element for which the total moles are to be calculated.
    - `dMolSum` (output double real): The calculated total number of moles of element `iElem` found in the specified solution species.

The subroutine works as follows:
1. It tokenizes the input `cCompName` string by commas to get individual phase names.
2. It iterates through all stable solution phases identified by Thermochimica.
3. For each stable solution phase, it checks if its name matches any of the names provided in `cCompName`.
4. If a match is found, it calculates the moles of the target element (`iElem`) in that phase using the phase's total moles (`dMolesPhase`) and the effective stoichiometry of the element in that phase (`dEffStoichSolnPhase`).
5. These amounts are summed up into `dMolSum`.

## Important Variables/Constants
- The subroutine relies on data stored in Thermochimica's common modules:
    - `nSolnPhases` (from `ModuleThermo`): Total number of solution phases in the system.
    - `iAssemblage` (from `ModuleThermo`): Array indicating stable phases; negative values are indices for solution phases.
    - `cSolnPhaseName` (from `ModuleThermo`): Array of solution phase names.
    - `dMolesPhase` (from `ModuleGEMSolver` or `ModuleThermo`): Array containing the total moles of each stable phase.
    - `dEffStoichSolnPhase` (from `ModuleThermo`): Array containing the effective stoichiometry of each element in each solution phase.

## Usage Examples
To use `FindSolnSpecies`, you would call it after a Thermochimica equilibrium calculation.

```fortran
USE ModuleThermoIO
USE ModuleThermo
USE ModuleGEMSolver ! Or wherever dMolesPhase is primarily managed post-solve
! Assuming an interface for FindSolnSpecies is available

implicit none

character(len=100) :: target_phases_str
integer :: element_Z, str_len
real(8) :: moles_of_element_in_phases

! Example: Calculate moles of Uranium (Z=92) in phases "GAS_PHASE" and "SLAG_PHASE"
target_phases_str = "GAS_PHASE,SLAG_PHASE"
str_len = len_trim(target_phases_str) ! For Fortran string
element_Z = 92

call FindSolnSpecies(target_phases_str, str_len, element_Z, moles_of_element_in_phases)

print *, "Total moles of element ", element_Z, " in specified phases: ", moles_of_element_in_phases

! Example for C-style string length (if applicable from an external call)
! target_phases_str = "GAS_PHASE,SLAG_PHASE"C ! Null-terminated
! str_len = actual_c_string_length ! Determined by strlen or similar
! call FindSolnSpecies(target_phases_str, str_len, element_Z, moles_of_element_in_phases)
```

## Dependencies and Interactions
- **Modules:**
    - `ModuleThermoIO`: Not directly used for variables in this routine, but often included in files that call API routines.
    - `ModuleThermo`: Provides `nSolnPhases`, `iAssemblage`, `cSolnPhaseName`, `dEffStoichSolnPhase`.
    - `ModuleGEMSolver`: Provides `dMolesPhase` (or it could be from `ModuleThermo` depending on the exact state of data).
- **Internal Subroutines:**
    - `tokenize(str, delim, word, lword, n)`: Called to parse the `cCompName` string. This subroutine is defined in `CouplingUtilities.f90`.
    - `matchdict(word, dictionary, nwords, lenword, imatch)`: Called to check if a solution phase name matches one from the tokenized list. This subroutine is also defined in `CouplingUtilities.f90`.
- **Assumptions:**
    - Assumes that Thermochimica has been run and the relevant data structures (`iAssemblage`, `dMolesPhase`, etc.) are populated.
    - The `tokenize` and `matchdict` subroutines must be accessible (e.g., by linking with `CouplingUtilities.o` or if they are in a shared module).
    - The maximum number of phase names to parse is hardcoded to 50 in `cPhase(50)` within `FindSolnSpecies`, which could be a limitation if `cCompName` contains more than 50 phase names.
