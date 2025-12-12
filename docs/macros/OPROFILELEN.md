# OPROFILELEN - Calcolo Lunghezza Profili Omega

## Panoramica

**OPROFILELEN** calcola la lunghezza totale dei profili omega necessari per rinforzare i pannelli del casing, in base alle dimensioni della sezione e al tipo di casing.

## Sintassi

```vb
OPROFILELEN() As Double
```

### Parametri

Nessun parametro richiesto. La macro legge automaticamente i dati dalla sezione corrente.

### Valori di Ritorno

Restituisce un valore `Double` che rappresenta la lunghezza totale dei profili omega in metri.

## Logica di Calcolo

### Variabili di Input

| Variabile | Fonte | Descrizione |
|-----------|-------|-------------|
| `iCasing` | `GD.CT.ID` | Tipo di casing (1-8) |
| `dLen` | `SE.OUT.Length` | Lunghezza sezione (mm) |
| `dHeight` | `SE.OUT.IntHeight` | Altezza interna (mm) |
| `dWidth` | `SE.OUT.IntWidth` | Larghezza interna (mm) |

### Formula Base

```vb
OPROFILELEN = OPROFILEQTA() * dLen / 1000
```

Dove:
- `OPROFILEQTA()`: Numero di profili omega necessari
- `dLen`: Lunghezza della sezione
- `/1000`: Conversione da mm a metri

## Calcolo Quantità Profili (OPROFILEQTA)

La quantità di profili omega dipende dal tipo di casing e dalle dimensioni:

### Casing Types 1-2 (P40 Series)
```vb
If dHeight <= 1225 And dWidth <= 1225 Then
    OPROFILEQTA = 0
ElseIf dHeight <= 1225 And dWidth > 1225 Then
    OPROFILEQTA = 2
ElseIf dHeight > 1225 And dWidth <= 1225 Then
    OPROFILEQTA = 2
Else
    OPROFILEQTA = 4
End If
```

### Casing Types 3, 6 (P60-46, P60-46 TB)
```vb
If dHeight <= 1225 And dWidth <= 1225 Then
    OPROFILEQTA = 0
ElseIf dHeight <= 1225 And dWidth > 1225 Then
    OPROFILEQTA = 2
ElseIf dHeight > 1225 And dWidth <= 1225 Then
    OPROFILEQTA = 2
Else
    OPROFILEQTA = 4
End If
```

### Casing Types 4, 7 (P60-60, P60-60 TB)
```vb
If dHeight <= 1530 And dWidth <= 1530 Then
    OPROFILEQTA = 0
ElseIf dHeight <= 1530 And dWidth > 1530 Then
    OPROFILEQTA = 2
ElseIf dHeight > 1530 And dWidth <= 1530 Then
    OPROFILEQTA = 2
Else
    OPROFILEQTA = 4
End If
```

### Casing Types 5, 8 (P60-80, P60-80 TB)
```vb
If dHeight <= 2140 And dWidth <= 2140 Then
    OPROFILEQTA = 0
ElseIf dHeight <= 2140 And dWidth > 2140 Then
    OPROFILEQTA = 2
ElseIf dHeight > 2140 And dWidth <= 2140 Then
    OPROFILEQTA = 2
Else
    OPROFILEQTA = 4
End If
```

## Logica di Rinforzo

### Nessun Rinforzo (0 profili)
Quando entrambe le dimensioni sono sotto la soglia del casing:
- P40: H ≤ 1225 mm AND W ≤ 1225 mm
- P60-46: H ≤ 1225 mm AND W ≤ 1225 mm
- P60-60: H ≤ 1530 mm AND W ≤ 1530 mm
- P60-80: H ≤ 2140 mm AND W ≤ 2140 mm

### Rinforzo Parziale (2 profili)
Quando solo una dimensione supera la soglia:
- Altezza > soglia, Larghezza ≤ soglia: 2 profili verticali
- Larghezza > soglia, Altezza ≤ soglia: 2 profili orizzontali

### Rinforzo Completo (4 profili)
Quando entrambe le dimensioni superano la soglia:
- 2 profili verticali + 2 profili orizzontali

## Esempi di Utilizzo

### Esempio 1: Sezione Piccola P40-30
```vb
' Configurazione:
' - Casing: P40-30 (Type 1)
' - Lunghezza: 1000 mm
' - Altezza: 920 mm
' - Larghezza: 920 mm

' Calcolo:
' OPROFILEQTA = 0 (entrambe dimensioni ≤ 1225)
' OPROFILELEN = 0 * 1000 / 1000 = 0 metri
```

### Esempio 2: Sezione Media P40-30
```vb
' Configurazione:
' - Casing: P40-30 (Type 1)
' - Lunghezza: 1500 mm
' - Altezza: 920 mm
' - Larghezza: 1530 mm

' Calcolo:
' OPROFILEQTA = 2 (larghezza > 1225, altezza ≤ 1225)
' OPROFILELEN = 2 * 1500 / 1000 = 3.0 metri
```

### Esempio 3: Sezione Grande P60-60
```vb
' Configurazione:
' - Casing: P60-60 (Type 4)
' - Lunghezza: 2000 mm
' - Altezza: 1840 mm
' - Larghezza: 1840 mm

' Calcolo:
' OPROFILEQTA = 4 (entrambe dimensioni > 1530)
' OPROFILELEN = 4 * 2000 / 1000 = 8.0 metri
```

### Esempio 4: Sezione P60-80
```vb
' Configurazione:
' - Casing: P60-80 (Type 5)
' - Lunghezza: 1800 mm
' - Altezza: 2450 mm
' - Larghezza: 1840 mm

' Calcolo:
' OPROFILEQTA = 2 (altezza > 2140, larghezza ≤ 2140)
' OPROFILELEN = 2 * 1800 / 1000 = 3.6 metri
```

## Tabella Riepilogativa Soglie

| Casing Type | Descrizione | Soglia Altezza | Soglia Larghezza |
|-------------|-------------|----------------|------------------|
| 1 | P40-30 | 1225 mm | 1225 mm |
| 2 | P40-45 | 1225 mm | 1225 mm |
| 3 | P60-46 | 1225 mm | 1225 mm |
| 4 | P60-60 | 1530 mm | 1530 mm |
| 5 | P60-80 | 2140 mm | 2140 mm |
| 6 | P60-46 TB | 1225 mm | 1225 mm |
| 7 | P60-60 TB | 1530 mm | 1530 mm |
| 8 | P60-80 TB | 2140 mm | 2140 mm |

## Note Importanti

### Unità di Misura
- Input: millimetri (mm)
- Output: metri (m)

### Orientamento Profili
La macro non distingue tra profili verticali e orizzontali nel calcolo della lunghezza totale. Tutti i profili hanno la stessa lunghezza pari alla lunghezza della sezione.

### Casing TB (Thermal Break)
I casing con thermal break (types 6-8) utilizzano le stesse soglie dei corrispondenti casing standard (types 3-5).

## Macro Correlate

- **OPROFILEQTA**: Calcolo quantità profili omega
- **PROFILELEN**: Calcolo lunghezza profili casing standard
- **PROFILEWIDTH**: Larghezza profilo casing

## Riferimenti

- **File**: `CBase.vb`
- **Classe**: `CBase`
- **Dipendenze**: Dati sezione (SE), Dati generali (GD)
