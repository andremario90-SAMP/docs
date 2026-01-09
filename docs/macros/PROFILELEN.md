# PROFILELEN - Calcolo Lunghezza Profili Casing

## Panoramica

**PROFILELEN** calcola la lunghezza totale dei profili necessari per il casing di una sezione, considerando il tipo di casing, le dimensioni e la configurazione dei pannelli.

## Sintassi

```vb
PROFILELEN() As Double
```

### Parametri

Nessun parametro richiesto. La macro legge automaticamente i dati dalla sezione corrente.

### Valori di Ritorno

Restituisce un valore `Double` che rappresenta la lunghezza totale dei profili in metri.

## Logica di Calcolo

### Variabili di Input

| Variabile | Fonte | Descrizione |
|-----------|-------|-------------|
| `iCasing` | `GD.CT.ID` | Tipo di casing (1-8) |
| `dLen` | `SE.OUT.Length` | Lunghezza sezione (mm) |
| `dHeight` | `SE.OUT.IntHeight` | Altezza interna (mm) |
| `dWidth` | `SE.OUT.IntWidth` | Larghezza interna (mm) |
| `iMatPos` | `SE.INP.MatPos` | Posizione materiale pannello |

### Formula Base

```vb
PROFILELEN = (2 * dHeight + 2 * dWidth + 4 * dLen) / 1000
```

Dove:
- `2 * dHeight`: Profili verticali (sinistro + destro)
- `2 * dWidth`: Profili orizzontali (superiore + inferiore)
- `4 * dLen`: Profili longitudinali (4 angoli)
- `/1000`: Conversione da mm a metri

### Correzioni per Tipo di Casing

#### Casing Type 1 (P40-30)
```vb
If iMatPos = 1 Then
    PROFILELEN = PROFILELEN + (4 * dLen) / 1000
End If
```

#### Casing Type 2 (P40-45)
```vb
If iMatPos = 1 Then
    PROFILELEN = PROFILELEN + (4 * dLen) / 1000
End If
```

#### Casing Types 3-8 (P60 Series)
Nessuna correzione aggiuntiva per pannelli interni.

## Esempi di Utilizzo

### Esempio 1: Sezione Standard P40-30
```vb
' Configurazione:
' - Casing: P40-30 (Type 1)
' - Lunghezza: 1000 mm
' - Altezza: 1225 mm
' - Larghezza: 920 mm
' - Pannello esterno (iMatPos = 0)

' Calcolo:
' PROFILELEN = (2*1225 + 2*920 + 4*1000) / 1000
' PROFILELEN = (2450 + 1840 + 4000) / 1000
' PROFILELEN = 8.29 metri
```

### Esempio 2: Sezione P40-30 con Pannello Interno
```vb
' Configurazione:
' - Casing: P40-30 (Type 1)
' - Lunghezza: 1000 mm
' - Altezza: 1225 mm
' - Larghezza: 920 mm
' - Pannello interno (iMatPos = 1)

' Calcolo:
' PROFILELEN = (2*1225 + 2*920 + 4*1000) / 1000 + (4*1000) / 1000
' PROFILELEN = 8.29 + 4.0
' PROFILELEN = 12.29 metri
```

### Esempio 3: Sezione P60-60
```vb
' Configurazione:
' - Casing: P60-60 (Type 4)
' - Lunghezza: 1500 mm
' - Altezza: 1530 mm
' - Larghezza: 1225 mm
' - Pannello interno (iMatPos = 1)

' Calcolo:
' PROFILELEN = (2*1530 + 2*1225 + 4*1500) / 1000
' PROFILELEN = (3060 + 2450 + 6000) / 1000
' PROFILELEN = 11.51 metri
' (Nessuna correzione per P60 con pannello interno)
```

## Note Importanti

### Unità di Misura
- Input: millimetri (mm)
- Output: metri (m)

### Pannelli Interni vs Esterni
- **Pannello Esterno** (`iMatPos = 0`): Calcolo standard
- **Pannello Interno** (`iMatPos = 1`): Aggiunge profili extra per casing P40

### Tipi di Profili
La macro calcola la lunghezza totale senza distinguere tra:
- Profili verticali
- Profili orizzontali
- Profili longitudinali

Per calcoli specifici per tipo di profilo, utilizzare macro dedicate.

## Macro Correlate

- **OPROFILELEN**: Calcolo lunghezza profili omega
- **OPROFILEQTA**: Calcolo quantità profili omega
- **PROFILEWIDTH**: Larghezza profilo casing

## Riferimenti

- **File**: `CBase.vb`
- **Classe**: `CBase`
- **Dipendenze**: Dati sezione (SE), Dati generali (GD)
