# OPROFILEQTA - Calcolo Quantità Profili Omega

## Panoramica

**OPROFILEQTA** calcola il numero di profili omega necessari per rinforzare i pannelli del casing in base alle dimensioni della sezione e al tipo di casing.

## Sintassi

```vb
OPROFILEQTA() As Integer
```

### Parametri

Nessun parametro richiesto. La macro legge automaticamente i dati dalla sezione corrente.

### Valori di Ritorno

Restituisce un valore `Integer` che rappresenta il numero di profili omega necessari:
- `0`: Nessun rinforzo necessario
- `2`: Rinforzo parziale (una direzione)
- `4`: Rinforzo completo (entrambe le direzioni)

## Logica di Calcolo

### Variabili di Input

| Variabile | Fonte | Descrizione |
|-----------|-------|-------------|
| `iCasing` | `GD.CT.ID` | Tipo di casing (1-8) |
| `dHeight` | `SE.OUT.IntHeight` | Altezza interna (mm) |
| `dWidth` | `SE.OUT.IntWidth` | Larghezza interna (mm) |

## Regole di Calcolo per Tipo di Casing

### Casing Types 1-2 (P40-30, P40-45)
**Soglia**: 1225 mm

```vb
If dHeight <= 1225 And dWidth <= 1225 Then
    Return 0  ' Nessun rinforzo
ElseIf dHeight <= 1225 And dWidth > 1225 Then
    Return 2  ' 2 profili orizzontali
ElseIf dHeight > 1225 And dWidth <= 1225 Then
    Return 2  ' 2 profili verticali
Else
    Return 4  ' 4 profili (2 vert + 2 oriz)
End If
```

### Casing Types 3, 6 (P60-46, P60-46 TB)
**Soglia**: 1225 mm

Stessa logica dei casing P40.

### Casing Types 4, 7 (P60-60, P60-60 TB)
**Soglia**: 1530 mm

```vb
If dHeight <= 1530 And dWidth <= 1530 Then
    Return 0  ' Nessun rinforzo
ElseIf dHeight <= 1530 And dWidth > 1530 Then
    Return 2  ' 2 profili orizzontali
ElseIf dHeight > 1530 And dWidth <= 1530 Then
    Return 2  ' 2 profili verticali
Else
    Return 4  ' 4 profili (2 vert + 2 oriz)
End If
```

### Casing Types 5, 8 (P60-80, P60-80 TB)
**Soglia**: 2140 mm

```vb
If dHeight <= 2140 And dWidth <= 2140 Then
    Return 0  ' Nessun rinforzo
ElseIf dHeight <= 2140 And dWidth > 2140 Then
    Return 2  ' 2 profili orizzontali
ElseIf dHeight > 2140 And dWidth <= 2140 Then
    Return 2  ' 2 profili verticali
Else
    Return 4  ' 4 profili (2 vert + 2 oriz)
End If
```

## Matrice Decisionale

| Altezza | Larghezza | Risultato | Descrizione |
|---------|-----------|-----------|-------------|
| ≤ Soglia | ≤ Soglia | 0 | Nessun rinforzo necessario |
| ≤ Soglia | > Soglia | 2 | 2 profili orizzontali |
| > Soglia | ≤ Soglia | 2 | 2 profili verticali |
| > Soglia | > Soglia | 4 | 2 verticali + 2 orizzontali |

## Esempi di Utilizzo

### Esempio 1: Sezione Piccola (Nessun Rinforzo)
```vb
' Casing: P40-30 (Type 1)
' Altezza: 920 mm (≤ 1225)
' Larghezza: 920 mm (≤ 1225)
' Risultato: 0 profili
```

### Esempio 2: Sezione Larga (Rinforzo Orizzontale)
```vb
' Casing: P40-30 (Type 1)
' Altezza: 920 mm (≤ 1225)
' Larghezza: 1530 mm (> 1225)
' Risultato: 2 profili (orizzontali)
```

### Esempio 3: Sezione Alta (Rinforzo Verticale)
```vb
' Casing: P60-60 (Type 4)
' Altezza: 1840 mm (> 1530)
' Larghezza: 1225 mm (≤ 1530)
' Risultato: 2 profili (verticali)
```

### Esempio 4: Sezione Grande (Rinforzo Completo)
```vb
' Casing: P60-60 (Type 4)
' Altezza: 1840 mm (> 1530)
' Larghezza: 1840 mm (> 1530)
' Risultato: 4 profili (2 vert + 2 oriz)
```

### Esempio 5: Sezione Molto Grande P60-80
```vb
' Casing: P60-80 (Type 5)
' Altezza: 2450 mm (> 2140)
' Larghezza: 2450 mm (> 2140)
' Risultato: 4 profili (2 vert + 2 oriz)
```

## Tabella Soglie per Tipo di Casing

| Casing Type | Descrizione | Soglia (mm) |
|-------------|-------------|-------------|
| 1 | P40-30 | 1225 |
| 2 | P40-45 | 1225 |
| 3 | P60-46 | 1225 |
| 4 | P60-60 | 1530 |
| 5 | P60-80 | 2140 |
| 6 | P60-46 TB | 1225 |
| 7 | P60-60 TB | 1530 |
| 8 | P60-80 TB | 2140 |

## Diagramma Decisionale

```
┌─────────────────────────────────────┐
│   Leggi Tipo Casing e Dimensioni   │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│  Determina Soglia per Tipo Casing  │
│  - P40: 1225 mm                     │
│  - P60-46: 1225 mm                  │
│  - P60-60: 1530 mm                  │
│  - P60-80: 2140 mm                  │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│  Altezza ≤ Soglia?                  │
└──────┬──────────────────┬───────────┘
       │ SI               │ NO
       ▼                  ▼
┌──────────────┐   ┌──────────────┐
│Larghezza     │   │Larghezza     │
│≤ Soglia?     │   │≤ Soglia?     │
└──┬───────┬───┘   └──┬───────┬───┘
   │SI     │NO        │SI     │NO
   ▼       ▼          ▼       ▼
  [0]     [2]        [2]     [4]
```

## Note Importanti

### Orientamento Profili
- **2 profili**: Posizionati nella direzione della dimensione che supera la soglia
- **4 profili**: 2 verticali + 2 orizzontali per rinforzo completo

### Casing TB (Thermal Break)
I casing con thermal break utilizzano le stesse soglie dei corrispondenti casing standard.

### Considerazioni Strutturali
Le soglie sono determinate da:
- Resistenza strutturale dei pannelli
- Normative di sicurezza
- Specifiche tecniche del produttore

## Macro Correlate

- **OPROFILELEN**: Calcolo lunghezza totale profili omega
- **PROFILELEN**: Calcolo lunghezza profili casing standard
- **PROFILEWIDTH**: Larghezza profilo casing

## Riferimenti

- **File**: `CBase.vb`
- **Classe**: `CBase`
- **Dipendenze**: Dati sezione (SE), Dati generali (GD)
