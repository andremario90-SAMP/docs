# Architecture

## System Architecture

ACN10058 follows a **modular class-based architecture** designed to extend the airCalc++ HVAC design software with custom calculation and pricing capabilities.

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    airCalc++ Host                        │
│                  (Main Application)                      │
└────────────────────┬────────────────────────────────────┘
                     │
                     │ Loads DLL
                     ▼
┌─────────────────────────────────────────────────────────┐
│                   ACN10058.DLL                           │
│  ┌───────────────────────────────────────────────────┐  │
│  │              CMain (Entry Point)                   │  │
│  │  - Startup() / EndUp() / Execute()                │  │
│  └───────────┬───────────────────────────────────────┘  │
│              │                                           │
│              │ Routes Commands to Specialized Classes   │
│              │                                           │
│  ┌───────────┴───────────────────────────────────────┐  │
│  │                                                     │  │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────────┐    │  │
│  │  │  CBase   │  │ CCommon  │  │CPricesAndWt  │    │  │
│  │  │          │  │          │  │              │    │  │
│  │  │ Geometry │  │ Utility  │  │ Pricing &    │    │  │
│  │  │ Calcs    │  │ Functions│  │ Weight Calcs │    │  │
│  │  └──────────┘  └──────────┘  └──────────────┘    │  │
│  │                                                     │  │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────────┐    │  │
│  │  │ CRfcMain │  │  CLib    │  │  CSelector   │    │  │
│  │  │          │  │          │  │              │    │  │
│  │  │ Refrig.  │  │ Library  │  │ UI Selection │    │  │
│  │  │ Systems  │  │ Functions│  │ Dialogs      │    │  │
│  │  └──────────┘  └──────────┘  └──────────────┘    │  │
│  │                                                     │  │
│  │  ┌──────────┐  ┌──────────┐                       │  │
│  │  │   CMSR   │  │  Utils   │                       │  │
│  │  │          │  │          │                       │  │
│  │  │ MSR/     │  │ Helper   │                       │  │
│  │  │ Stats    │  │ Utilities│                       │  │
│  │  └──────────┘  └──────────┘                       │  │
│  └─────────────────────────────────────────────────────┘  │
│                                                           │
│  ┌─────────────────────────────────────────────────────┐  │
│  │         External Dependencies                        │  │
│  │  - AclMkrTools (Macro Tools)                        │  │
│  │  - AclMkWrp (Macro Wrapper)                         │  │
│  │  - ADODB (Database Access)                          │  │
│  │  - Newtonsoft.Json (JSON Processing)                │  │
│  └─────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────┘
```

## Component Interaction Flow

### 1. Initialization Flow

```
airCalc++ Startup
    ↓
Load ACN10058.DLL
    ↓
CMain.Startup()
    ↓
Initialize MacroWrapper for all classes
    ↓
Load Caches (Materials, Weights, Prices, Times)
    ↓
Ready for Command Execution
```

### 2. Command Execution Flow

```
airCalc++ Issues Command
    ↓
CMain.Execute(sCommand, oArg, bFound)
    ↓
Parse Command Prefix (B_, P_, RFC_, LIB_, SEL_, MSR)
    ↓
Route to Appropriate Class
    ↓
Execute Method via CallByName
    ↓
Return Result to airCalc++
```

### 3. Shutdown Flow

```
airCalc++ Shutdown
    ↓
CMain.EndUp()
    ↓
Cleanup Class Pointers
    ↓
CMain.Finalize()
    ↓
Garbage Collection
```

## Command Routing System

The `CMain.Execute()` method implements a **prefix-based routing system**:

| Prefix | Target Class | Purpose |
|--------|-------------|---------|
| `B_` | CBase | Base geometry and dimension calculations |
| `P_` | CPricesAndWeight | Pricing and weight calculations |
| `RFC_` | CRfcMain | Refrigeration system calculations |
| `FcuREF_` | CRfcMain | FCU refrigeration reference |
| `LIB_` | CLib | Library functions |
| `SEL_` | CSelector | User selection dialogs |
| `MSR` | CMSR | MSR/Statistics functions |
| (none) | CCommon | Common utility functions |

**Example**: 
- Command `B_OPDIF` → Routes to `CBase.OPDIF()`
- Command `P_DRAINPAN` → Routes to `CPricesAndWeight.DRAINPAN()`
- Command `RFC_InterfaceGetFields` → Routes to `CRfcMain.InterfaceGetFields()`

## Data Flow Architecture

### Input Sources
1. **airCalc++ Macro System**: Commands and parameters from the host application
2. **Database Tables**: Material specifications, pricing data, component catalogs
3. **User Input**: Selection dialogs and configuration forms
4. **External APIs**: Project management and statistics systems

### Processing Layers
1. **Command Router** (CMain): Directs commands to appropriate handlers
2. **Business Logic** (Specialized Classes): Performs calculations and transformations
3. **Data Access** (Cache + Database): Retrieves and stores data
4. **Utility Layer** (Utils, Singleton): Provides helper functions

### Output Destinations
1. **airCalc++ Fields**: Calculated values returned to host application
2. **Database**: Updated pricing, weight, and configuration data
3. **External Systems**: JSON-based API calls for project synchronization
4. **User Interface**: Dialog results and error messages

## Design Patterns

### 1. Singleton Pattern
- **File**: `Singleton.vb`
- **Purpose**: Maintains shared state across macro executions

### 2. Facade Pattern
- **Class**: `CMain`
- **Purpose**: Provides unified interface to complex subsystem of specialized classes

### 3. Strategy Pattern
- **Classes**: CBase, CPricesAndWeight, CRfcMain
- **Purpose**: Encapsulates different calculation algorithms

### 4. Caching Pattern
- **Class**: `CPricesAndWeight`
- **Purpose**: Improves performance by caching frequently accessed database data
- **Cached Data**: Materials (`mMatKg`), Weights (`mWeight`), Prices (`mPrice`), Times (`mTime`)

## Memory Management

The library implements explicit memory management:

1. **Startup**: Initialize MacroWrapper pointers for all classes
2. **Runtime**: Maintain references to airCalc++ objects
3. **Cleanup**: 
   - `EndUp()`: User-initiated cleanup
   - `Finalize()`: Automatic cleanup on disposal
   - Explicit `Nothing` assignments
   - `GC.Collect()`: Force garbage collection

## Error Handling

- **Try-Catch Blocks**: Wrap command execution
- **MissingMemberException**: Handles undefined macro calls
- **Message Boxes**: User-facing error notifications
- **Debug Output**: Console logging for development

## Integration Points

### airCalc++ Integration
- **MacroWrapper**: `airCalcMacroWrapper.CAclMkrHelper`
- **Access Points**: 
  - `O.Mkr`: Core macro functions
  - `O.Mtu`: Multi-unit operations
  - `O.Hlp`: Helper utilities
  - `O.Scrp`: Script generation

### Database Integration
- **Technology**: ADO (ActiveX Data Objects)
- **Tables**: tblMat, tblWeights, tblPrices, tblManTimes, tblCasing, and others
- **Operations**: SELECT queries, data caching, recordset manipulation

### External API Integration
- **Protocol**: HTTPS/JSON
- **Endpoint**: `https://samp.clv.it/api_idOfferta2.php`
- **Purpose**: Project synchronization and statistics
- **Security**: Token-based authentication
