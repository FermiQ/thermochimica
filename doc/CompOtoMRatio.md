## Overview
The `CompOtoMRatio.f90` file provides a subroutine to compute the oxygen-to-metal (O/M) ratio specifically for the UO2 solid solution phase within the Thermochimica system. This calculation is important for understanding the stoichiometry of this phase, which can change due to factors like fission product dissolution.

## Key Components
- `CompOtoMRatio(cPhaseIn, lcPhaseIn, dOtoMRatio, INFO)`: This is the main subroutine.
    - `cPhaseIn` (input character string): The name of the UO2 solid solution phase.
    - `lcPhaseIn` (input integer): The length of the `cPhaseIn` string.
    - `dOtoMRatio` (output double real): The calculated oxygen-to-metal ratio.
    - `INFO` (output integer): Status indicator (0 for success, non-zero for error).
    - The subroutine identifies the specified phase and calculates the O/M ratio based on site fractions in a three-sublattice model (cations, normal oxygen anions/vacancies, interstitial oxygen anions/vacancies).

## Important Variables/Constants
- There are no globally defined constants in this file, but the logic relies heavily on the internal structure and naming of phases (specifically, expecting a 3-sublattice model for the UO2-type phase).

## Usage Examples
To use this subroutine, you would call it from a Fortran program after a Thermochimica calculation has been performed and the UO2 phase is stable.

```fortran
USE ModuleThermo ! To access nSolnPhases, iAssemblage, cSolnPhaseName, etc.
implicit none
character(25) :: phase_name
integer :: info_val, phase_name_len
real(8) :: o_to_m_ratio

phase_name = 'UO2_SOLID_SOLUTION_PHASE_NAME' ! Replace with actual phase name
phase_name_len = len_trim(phase_name)

call CompOtoMRatio(phase_name, phase_name_len, o_to_m_ratio, info_val)

if (info_val == 0) then
    print *, 'O/M Ratio: ', o_to_m_ratio
else
    print *, 'Error calculating O/M Ratio.'
end if
```

## Dependencies and Interactions
- **Modules:** Uses `ModuleThermo` for accessing thermodynamic data like `nSolnPhases`, `iAssemblage`, `cSolnPhaseName`, `cSolnPhaseType`, `iPhaseSublattice`, `nSublatticePhase`, and `dSiteFraction`.
- **Assumptions:**
    - Assumes the UO2 phase is represented by a three-sublattice compound energy formalism model (SUBL or SUBLM type).
    - The first sublattice is for cations, the second for normal oxygen sites (or vacancies), and the third for interstitial oxygen sites (or vacancies).
    - The calculation `dOtoMRatio = 2D0 * dSiteFraction(iChargedPhaseID,2,1) + dSiteFraction(iChargedPhaseID,3,1)` is specific to this 3-sublattice definition where the second sublattice site fraction for oxygen (index 1) contributes twice to the oxygen count and the third sublattice site fraction for interstitial oxygen (index 1) contributes once.
- **Limitations:** The file itself notes a TODO to make the subroutine more general, as its current implementation is specific to this UO2 model.
