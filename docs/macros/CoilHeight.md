# CoilHeight - Calcolo Altezza Batteria

## Panoramica

**CoilHeight** calcola l'altezza effettiva della batteria (coil) in base alle dimensioni interne della sezione e al tipo di casing.

## Sintassi

```vb
CoilHeight() As Double
```

### Parametri

Nessun parametro richiesto. La macro legge automaticamente i dati dalla sezione corrente.

### Valori di Ritorno

Restituisce un valore `Double` che rappresenta l'altezza della batteria in millimetri (mm).

## Logica di Calcolo

### Variabili di Input

| Variabile | Fonte | Descrizione |
|-----------|-------|-------------|
| `iCasing` | `GD.CT.ID` | Tipo di casing (1-8) |
| `dHeight` | `SE.OUT.IntHeight` | Altezza interna sezione (mm) |

### Formula per Tipo di Casing

#### Casing Types 1-2 (P40 Series)
```vb
CoilHeight = dHeight - 40
```

#### Casing Types 3-8 (P60 Series)
```vb
CoilHeight = dHeight - 60
```

## Spiegazione

L'altezza della batteria è ridotta rispetto all'altezza interna della sezione per tenere conto di:
- Spessore profili superiore e inferiore
- Spazio per guide e supporti
- Tolleranze di montaggio

### Riduzione per Serie
- **P40**: -40 mm (profili da 40 mm)
- **P60**: -60 mm (profili da 60 mm)

## Esempi di Utilizzo

### Esempio 1: Sezione P40-30
```vb
' Configurazione:
' - Casing: P40-30 (Type 1)
' - Altezza interna: 920 mm

' Calcolo:
' CoilHeight = 920 - 40 = 880 mm
```

### Esempio 2: Sezione P60-60
```vb
' Configurazione:
' - Casing: P60-60 (Type 4)
' - Altezza interna: 1530 mm

' Calcolo:
' CoilHeight = 1530 - 60 = 1470 mm
```

### Esempio 3: Sezione P60-80 TB
```vb
' Configurazione:
' - Casing: P60-80 TB (Type 8)
' - Altezza interna: 2450 mm

' Calcolo:
' CoilHeight = 2450 - 60 = 2390 mm
```

## Tabella Riepilogativa

| Casing Type | Descrizione | Riduzione (mm) |
|-------------|-------------|----------------|
| 1 | P40-30 | 40 |
| 2 | P40-45 | 40 |
| 3 | P60-46 | 60 |
| 4 | P60-60 | 60 |
| 5 | P60-80 | 60 |
| 6 | P60-46 TB | 60 |
| 7 | P60-60 TB | 60 |
| 8 | P60-80 TB | 60 |

## Note Importanti

### Unità di Misura
- Input: millimetri (mm)
- Output: millimetri (mm)

### Dimensioni Standard Batterie
Le batterie sono disponibili in dimensioni standard. L'altezza calcolata deve corrispondere a una dimensione disponibile a catalogo.

### Considerazioni di Progetto
- Verificare disponibilità dimensione batteria
- Considerare spazio per guide e supporti
- Valutare accessibilità per manutenzione

## Applicazioni

Questa macro è utilizzata per:
- Selezione batterie da catalogo
- Calcolo superficie di scambio termico
- Verifica compatibilità dimensionale
- Generazione disegni tecnici

## Macro Correlate

- **CoilWidth**: Calcolo larghezza batteria
- **PROFILEWIDTH**: Larghezza profilo casing
- **WTFrm**: Calcolo altezza telaio batteria

## Riferimenti

- **File**: `CBase.vb`
- **Classe**: `CBase`
- **Dipendenze**: Dati sezione (SE), Dati generali (GD)
