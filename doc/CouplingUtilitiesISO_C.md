## Overview
The `CouplingUtilitiesISO_C.f90` file provides a C-compatible interface to a subset of Thermochimica's Fortran subroutines, particularly many of those found in `CouplingUtilities.f90` and core Thermochimica routines. It uses the `ISO_C_BINDING` module to define functions that can be directly called from C or C++ code. These wrappers handle the conversion of C data types (like strings) to their Fortran equivalents and then call the underlying Fortran implementation. This file is crucial for enabling the integration of Thermochimica into C/C++ based applications.

## Key Components
This file defines numerous C-callable wrapper functions. Each wrapper typically:
- Uses `ISO_C_BINDING` for C interoperability.
- Has a `bind(C, name="TCAPI_...")` attribute, defining the C function name (e.g., `TCAPI_setThermoFilename`).
- Converts C string arguments (character arrays and lengths) to Fortran strings using `c_f_pointer`.
- Calls the corresponding Fortran subroutine (e.g., `SetThermoFileNameISO` calls `SetThermoFileName`).

Key wrapped functionalities include (grouped by the C API prefix `TCAPI_`):
- **Setup and Configuration:**
    - `TCAPI_setThermoFilename`: Wraps `SetThermoFileName`.
    - `TCAPI_setUnitTemperature`, `TCAPI_setUnitPressure`, `TCAPI_setUnitMass`: Wrap `SetUnitTemperature`, `SetUnitPressure`, `SetUnitMass`.
    - `TCAPI_setStandardUnits`, `TCAPI_setModelicaUnits`: Wrap `SetStandardUnits`, `SetModelicaUnits`.
    - `TCAPI_setUnits`: Wraps `SetUnits`.
    - `TCAPI_setTemperaturePressure`: Wraps `SetTemperaturePressure`.
    - `TCAPI_setPrintResultsMode`: Wraps `SetPrintResultsMode`.
    - `TCAPI_presetElementMass`, `TCAPI_setElementMass`: Wrap `PresetElementMass`, `SetElementMass`.
    - `TCAPI_setReinitRequested`: Wraps `SetReinitRequested`.
    - `TCAPI_setHeatCapacityEnthalpyEntropyRequested`: Wraps `SetHeatCapacityEnthalpyEntropyRequested`.
    - `TCAPI_setFuzzyStoich`, `TCAPI_setFuzzyMagnitude`: Wrap `SetFuzzyStoich`, `SetFuzzyMagnitude`.
    - `TCAPI_setGibbsMinCheck`: Wraps `SetGibbsMinCheck`.
- **Core Thermochimica Operations:**
    - `TCAPI_parseCSDataFile`: Wraps `ParseCSDataFile`.
    - `TCAPI_thermochimica`: Wraps `Thermochimica`.
    - `TCAPI_setup`: Wraps `ThermochimicaSetup`.
    - `TCAPI_solve`: Wraps `ThermochimicaSolver`.
    - `TCAPI_checkSystem`: Wraps `CheckSystem`.
    - `TCAPI_init`: Wraps `InitThermo`.
    - `TCAPI_compThermoData`: Wraps `CompThermoData`.
- **Reset Operations:**
    - `TCAPI_resetInfoThermo`: Wraps `ResetINFOThermo`.
    - `TCAPI_resetThermo`: Wraps `ResetThermo`.
    - `TCAPI_resetThermoAll`: Wraps `ResetThermoAll`.
    - `TCAPI_resetReinit`: Wraps `ResetReinit`.
- **Data Retrieval (System Information & Results):**
    - `TCAPI_getNumberElementsDatabase`: Accesses `nElementsCS` from `ModuleParseCS`.
    - `TCAPI_getElementAtIndex`: Accesses `cElementNameCS` from `ModuleParseCS`.
    - `TCAPI_getElementMass`: Wraps `GetElementMass`.
    - `TCAPI_checkInfoThermo`: Wraps `CheckINFOThermo`.
    - `TCAPI_getNumberPhasesSystem`: Wraps `GetNumberPhasesSystem`.
    - `TCAPI_getPhaseNameAtIndex`: Accesses `cSolnPhaseName` or `cSpeciesName` from `ModuleThermo`.
    - `TCAPI_getNumberSpeciesSystem`: Wraps `GetNumberSpeciesSystem`.
    - `TCAPI_getSpeciesAtIndex`: Accesses `cSpeciesName` from `ModuleThermo`.
    - `TCAPI_isPhaseGas`, `TCAPI_isPhaseMQM`: Check `cSolnPhaseType` from `ModuleThermo`.
    - `TCAPI_getMqmqaPairAtIndex`: Accesses `cPairName` from `ModuleThermo`.
    - `TCAPI_solPhaseParse`: Wraps `SolPhaseParse`.
    - `TCAPI_getMolFraction`: Wraps `getMolFraction`.
    - `TCAPI_getChemicalPotential`: Wraps `getChemicalPotential`.
    - `TCAPI_getElementPotential`: Wraps `getElementPotential`.
    - `TCAPI_getReinitDataSizes`: Wraps `getReinitDataSizes`.
    - `TCAPI_getReinitData`, `TCAPI_setReinitData`: Wrap `getReinitData`, `setReinitData`.
    - `TCAPI_getMolesPhase`: Wraps `GetMolesPhase`.
    - `TCAPI_getAssemblage`: Wraps `GetAssemblage`.
    - `TCAPI_getAllElementPotential`: Wraps `GetAllElementPotential`.
    - `TCAPI_getElementFraction`: Wraps `GetElementFraction`.
    - `TCAPI_getHeatCapacityEnthalpyEntropy`: Wraps `GetHeatCapacityEnthalpyEntropy`.
    - `TCAPI_getElementMoleFractionInPhase`, `TCAPI_getElementMolesInPhase`: Wrap `GetElementMoleFractionInPhase`, `GetElementMolesInPhase`.
    - `TCAPI_getOutputChemPot`: Wraps `GetOutputChemPot`.
    - `TCAPI_getOutputMolSpecies`, `TCAPI_getOutputMolSpeciesPhase`: Wrap `GetOutputMolSpecies`, `GetOutputMolSpeciesPhase`.
    - `TCAPI_getOutputSiteFraction`: Wraps `GetOutputSiteFraction`.
    - `TCAPI_getOutputSolnSpecies`: Wraps `GetOutputSolnSpecies`.
    - `TCAPI_getPhaseIndex`: Wraps `GetPhaseIndex`.
    - `TCAPI_getPureConPhaseMol`: Wraps `GetPureConPhaseMol`.
    - `TCAPI_getSublSiteMol`: Wraps `GetSUBLSiteMol`.
    - `TCAPI_getSolnPhaseMol`: Wraps `GetSolnPhaseMol`.
    - `TCAPI_getMqmqaMolesPairs`, `TCAPI_getMqmqaPairMolFraction`, `TCAPI_getMqmqaNumberPairsQuads`, `TCAPI_getMqmqaConstituentFraction`: Wrap corresponding MQMQA functions.
- **Debugging and State:**
    - `TCAPI_thermoDebug`: Wraps `ThermoDebug`.
    - `TCAPI_printResults`: Wraps `PrintResults`.
    - `TCAPI_saveReinitData`: Wraps `SaveReinitData`.
    - `TCAPI_printState`: Wraps `PrintState`.

## Important Variables/Constants
This file itself does not define global constants. It acts as an interface to Fortran modules like `ModuleThermoIO`, `ModuleThermo`, `ModuleParseCS`, and `ModuleReinit`, which house the actual data and state variables. The C functions receive and return C-compatible data types (e.g., `c_char`, `c_int`, `c_double`, `c_ptr`, `c_bool`).

## Usage Examples
These functions are intended to be called from C or C++ code. A C header file (e.g., `thermochimica_api.h`) would typically declare these `TCAPI_` functions.

```c
// Example C code (conceptual)
#include "thermochimica_api.h" // Assuming this header declares the TCAPI_ functions
#include <stdio.h>
#include <string.h>

int main() {
    char db_file[] = "path/to/your/data.dat";
    TCAPI_setThermoFilename(db_file, strlen(db_file));
    TCAPI_setStandardUnits();

    TCAPI_parseCSDataFile();
    int info;
    TCAPI_checkInfoThermo(&info);
    if (info != 0) {
        printf("Error parsing data file: %d\n", info);
        return 1;
    }

    double temp = 1273.15;
    double press = 1.0;
    TCAPI_setTemperaturePressure(temp, press);

    // ... Set element masses using TCAPI_setElementMass ...

    TCAPI_thermochimica();
    TCAPI_checkInfoThermo(&info);
    if (info != 0) {
        printf("Error in thermochimica calculation: %d\n", info);
        return 1;
    }

    int n_soln_phases, n_con_phases;
    TCAPI_getNumberPhasesSystem(&n_soln_phases, &n_con_phases);
    printf("Solution phases: %d, Condensed phases: %d\n", n_soln_phases, n_con_phases);

    // ... Further calls to retrieve results ...

    TCAPI_resetThermoAll();
    return 0;
}
```

## Dependencies and Interactions
- **Fortran Modules:**
    - `ISO_C_BINDING`: Standard Fortran module for C interoperability.
    - `ModuleThermoIO`, `ModuleThermo`, `ModuleParseCS`, `ModuleReinit`: These Fortran modules contain the variables and core logic that these C wrappers ultimately interact with.
- **Wrapped Fortran Subroutines:** This file extensively calls subroutines from `CouplingUtilities.f90` (e.g., `SetThermoFileName`, `GetElementMass`), core Thermochimica files (e.g., `Thermochimica`, `ParseCSDataFile`, `ResetThermoAll`), and various result-getter functions from other API files (e.g., `GetElementMoleFractionInPhase`, `GetOutputChemPot`).
- **C Code:** Designed to be compiled and linked with C/C++ application code, which would call these `TCAPI_` prefixed functions.
- **String Handling:** A key role of these wrappers is to correctly handle character strings passed between C (null-terminated arrays of chars) and Fortran (fixed-length character variables or assumed-length). This is often done using `c_f_pointer` and passing explicit string lengths.
