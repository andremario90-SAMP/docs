# Modules and Components

## Core Modules

### 1. CMain.vb
**Entry Point and Command Router**

The main orchestrator class that manages the lifecycle and command routing for the entire DLL.

**Key Responsibilities**:
- Initialize and manage all specialized classes
- Route commands based on prefix to appropriate handlers
- Manage MacroWrapper pointers
- Handle startup and shutdown sequences

**Key Methods**:
- `Startup()`: Initialize all classes and load caches
- `Execute(sCommand, oArg, bFound)`: Route commands to specialized classes
- `EndUp()`: Cleanup on application close
- `Finalize()`: Memory cleanup and garbage collection

**Command Routing**:
```vb
B_*       → CBase
P_*       → CPricesAndWeight
RFC_*     → CRfcMain
FcuREF_*  → CRfcMain
LIB_*     → CLib
SEL_*     → CSelector
MSR*      → CMSR
(default) → CCommon
```

---

### 2. CBase.vb
**Geometry and Dimensional Calculations**

Handles geometric calculations, dimensional adjustments, and component positioning for HVAC units.

**Key Functions**:
- `OPDIF()`: Calculate opening differences for dampers and components
- `OPDIF2021()`: Updated opening difference calculations (2021 version)
- `OPDIF_BASE()`: Base opening difference calculations
- `PROFILELEN()`: Calculate profile lengths for casing
- `OPROFILELEN()`: Calculate omega profile lengths
- `OPROFILEQTA()`: Determine quantity of omega profiles needed
- `PROFILEWIDTH()`: Get casing profile width
- `CoilHeight()`: Calculate coil height based on section dimensions
- `CoilWidth()`: Calculate coil width based on section dimensions
- `AspirationLenght1()`, `AspirationLenght2()`: Calculate aspiration section lengths
- `MixBoxLenght()`: Calculate mixing box section length
- `PTDLEN()`: Calculate plate heat exchanger diagonal length
- `MinInDistVF()`: Minimum inlet distance for ventilation fans
- `WTFrm()`: Calculate frame heights for coils
- `DropletEliminatorHeight()`: Calculate droplet eliminator dimensions
- `SoundAttenuatorMaxVel()`: Calculate maximum velocity for sound attenuators
- `FilterLayoutTD()`: Generate filter layout descriptions
- `FilterLayoutExtraLengh()`: Calculate extra length for staggered filters
- `TOTALHEIGHT()`: Calculate total unit height including base and feet
- `A2G25CHECK()`: Check for A2G-25 component presence
- `CADEXP()`: Export CAD geometry data to JSON

**Supported Casing Types**:
- P40-30 (Case 1)
- P40-45 (Case 2)
- P60-46 / P60-46 TB (Cases 3, 6)
- P60-60 / P60-60 TB (Cases 4, 7)
- P60-80 / P60-80 TB (Cases 5, 8)

---

### 3. CPricesAndWeight.vb
**Pricing and Weight Calculations**

Manages all pricing, weight, and material calculations with database caching for performance.

**Key Features**:
- **Caching System**: In-memory caches for materials, weights, prices, and manufacturing times
- **Bill of Materials**: Generates component lists with quantities and waste factors
- **Material Calculations**: Computes sheet metal areas, profile lengths, and material usage

**Cache Management**:
- `LOADMATCACHE()`: Load material specifications
- `LOADWEIGHTCACHE()`: Load weight data
- `LOADPRICECACHE()`: Load pricing data
- `LOADTIMECACHE()`: Load manufacturing time data

**Calculation Functions**:
- `DRAINPAN()`: Drain pan pricing and weight
- `DROPELIMINATOR()`: Droplet eliminator calculations
- `BASEFRAME()`: Base frame material and cost
- `BASEFOOTPARTS()`: Unit foot components
- `INTAKEHOOD()`, `OUTLETHOOD()`: Hood calculations
- `FLEXPARTS()`: Flexible canvas components
- `DAMPER()`: Damper pricing and materials
- `COILRAILS()`: Coil support rail calculations
- `PANEL()`, `PANELQM()`: Panel material calculations
- `PANELQMNEW()`, `PANCODENEW()`: New panel calculation methods
- `PANMANTIME()`: Panel manufacturing time
- `FILTERAILSTF()`, `FILTERAILSFH()`, `FILTERAILSAF()`: Filter rail calculations
- `FANMOTORFIXINGPARTS()`: Fan motor mounting components
- `SOUNDATTENUATORSET()`: Sound attenuator components
- `HEATWHEEL()`: Heat wheel support structures
- `WSGPRICE()`: Weather louver pricing
- `GRAVDPRICE()`: Gravity damper pricing
- `RAUPRICE()`: Connection frame pricing
- `EST()`: Anti-vibration connection frame
- `FREEZERAILS()`: Frost protection frame
- `ROOFDATA()`: Roof panel calculations
- `PULDrive()`: Belt drive components
- `ASOPREIS()`: Isolator pricing
- `WIRESIZE()`, `WIRELEN()`: Electrical wiring calculations
- `DIFFUSER()`: Diffuser components
- `BURNER()`: Direct burner components
- `PLUGFANFIXINGPARTS()`: Plug fan mounting
- `PLATEEXCHANGERGUIDES()`: Plate heat exchanger supports
- `M2WALL()`: Mixing section wall
- `ANTISLIDE()`: Anti-sliding sheet
- `CwbPrice()`: Honeycomb humidifier pricing
- `LwNozzle()`, `LwDrainPan()`: Air washer components
- `FilterFrameAbsolute()`: Absolute filter frame
- `FOOT()`: Unit foot calculations

**Helper Functions**:
- `i_MATID()`: Get material ID
- `i_MATCOD()`: Get material code
- `i_MATTXT()`: Get material description
- `i_MATKG()`: Get material weight per m²
- `i_GetWeight()`, `i_GetPrice()`, `i_GetTime()`: Cache lookup functions
- `i_LISTRES()`: Process bill of materials and return totals

**Waste Factors**:
```vb
madOffCut = {0.0, 3.0, 5.0, 10.0, 30.0}
```

---

### 4. CCommon.vb
**Common Utility Functions**

Provides shared utility functions, project management, and external API integration.

**Key Functions**:
- `DS()`: Retrieve delivery section information
- `HRSEFF()`: Get heat recovery system efficiency
- `FANFIELD()`: Find fan data in units
- `iRepQTa()`: Calculate quantity of isolator switches
- `TD_COIL()`: Get coil technical drawing data
- `SETVARPOSFIELDSEX()`: Set position header fields with inverter/REP data
- `WIZ00_FormBeforeSave()`: Validation before saving general data
- `ToVal()`: String to double conversion
- `CheckIntConn()`: Check internet connectivity
- `EXECUTEJOB()`: Handle project save/delete events
- `GETNEWPROJECTNUMBER()`: Get new project number from external API
- `UPDPROJECTSTAT()`: Update project statistics via API
- `TDSAskForPrintoutFlags()`: Manage technical data printout options
- `AfterSaveSeriesData()`: Post-save processing
- `OVERWRITEPFACTORS1()`: Determine price factors based on unit type

**External API Integration**:
- `JsonGetProNumber`: JSON structure for new project requests
- `JsonUpdateProject`: JSON structure for project updates
- `SendRequest()`: HTTP POST request handler
- Endpoint: `https://samp.clv.it/api_idOfferta2.php`

---

### 5. CRfcMain.vb
**Refrigeration System Calculations**

Handles refrigeration circuit calculations and external interface for refrigeration data exchange.

**Key Functions**:
- `InterfaceGetFields()`: Export refrigeration data to external systems
- `InterfaceCalcSpecial()`: Perform specialized refrigeration calculations
- `InterfaceGetFieldsResult()`: Return calculation results
- `ImageName()`: Select refrigeration system diagram
- `ConfigureINPFields_DX()`: Configure input fields for DX coil
- `ConfigureINPFields_CX()`: Configure input fields for condenser coil
- `ConfigureINPFields_REF()`: Configure refrigerant main data fields
- `ConfigureINPFields_CMP()`: Configure compressor circuit fields
- `ConfigureOUTFields_DX()`: Configure output fields for DX coil
- `ConfigureOUTFields_CX()`: Configure output fields for condenser
- `ConfigureOUTFields_REF()`: Configure output fields for refrigerant data
- `ConfigureOUTFields_CMP()`: Configure output fields for compressor

**Data Exported**:
- Refrigerant type and circuits
- Compressor quantities and capacities
- Evaporator/condenser specifications
- Pipe lengths and splits
- Individual compressor details (up to 4 units)

---

### 6. CSelector.vb
**User Interface and Selection Dialogs**

Manages interactive selection dialogs and bulk editing operations.

**Key Functions**:
- `EXECUTE()`: Main entry point for bulk edit operations
- `ChangeRSLen()`: Change module step length (10mm, 50mm, 90mm)
- `CheckBlocked()`: Verify locked components and manual edits
- `MatChangeExh()`: Change material for exhaust air sections
- `MatResetExh()`: Reset materials to defaults
- `ChangeLamp()`: Change lamp type across all sections
- `ChangeDamper()`: Change damper type globally
- `AddFC()`: Add frequency converters to fan sections
- `MatChangePos()`: Select material position (internal/external/bottom/rails/insulation)
- `MatChangeTyp()`: Select material type
- `MatChangeThk()`: Select material thickness
- `MatChangeCol()`: Select material color

**Supported Operations**:
1. Set module step length
2. Check blocked components
3. Change section materials in exhaust air
4. Reset section materials to defaults
5. Change lamp types
6. Change damper types
7. Insert lamps

---

### 7. CLib.vb
**Library Functions**

Provides library-level utility functions (implementation details not fully visible in provided code).

**Purpose**: General-purpose library functions accessible via `LIB_` prefix.

---

### 8. CMSR.vb
**MSR and Statistics**

Handles measurement, statistics, and reporting functions (implementation details not fully visible in provided code).

**Purpose**: MSR (Measurement, Statistics, Reporting) operations accessible via `MSR` prefix.

---

### 9. CStatistics.vb
**Statistical Analysis**

Provides statistical analysis and reporting capabilities (implementation details not fully visible in provided code).

---

## Supporting Files

### Utils.vb
**Utility Helper Functions**

Provides general-purpose helper functions used across multiple modules.

### Singleton.vb
**Singleton Pattern Implementation**

Maintains shared state and provides global access point for shared data.

**Usage**: 
```vb
Singleton.GetInstance.Feld(index)
```

### frmSelect.vb / frmSelect.designer.vb
**Selection Form UI**

Windows Forms dialog for user selections in bulk edit operations.

**Features**:
- DataTable-based selection lists
- Customizable columns (ID, Name)
- Return selected value to caller

## Module Dependencies

```
CMain
  ├── CCommon
  ├── CBase
  ├── CPricesAndWeight
  │     └── CBase (for helper functions)
  ├── CRfcMain
  ├── CLib
  ├── CSelector
  │     └── frmSelect (UI)
  └── CMSR

Utils (used by multiple modules)
Singleton (global state)
```

## Configuration Files

### My Project/AssemblyInfo.vb
Assembly metadata and version information.

### My Project/Application.Designer.vb
Application-level settings and configuration.

### My Project/Resources.Designer.vb
Embedded resources management.

### My Project/Settings.Designer.vb
Application settings and user preferences.

## Build Configuration

- **Debug Output**: `..\..\..airCalc\Engines\`
- **Release Output**: `..\..\..airCalc\Engines\`
- **Signing**: Strong-named assembly with `IscAclSign.snk`
- **Platform**: x86 (32-bit)
- **Documentation**: XML documentation file generated (`ACN10058.xml`)
