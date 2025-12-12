# PROFILEWIDTH - Larghezza Profilo Casing

## Panoramica

**PROFILEWIDTH** restituisce la larghezza del profilo del casing in base al tipo di casing selezionato.

## Sintassi

```vb
PROFILEWIDTH() As Double
```

### Parametri

Nessun parametro richiesto. La macro legge automaticamente il tipo di casing dai dati generali.

### Valori di Ritorno

Restituisce un valore `Double` che rappresenta la larghezza del profilo in millimetri (mm).

## Larghezze per Tipo di Casing

| Casing Type | Descrizione | Larghezza Profilo (mm) |
|-------------|-------------|------------------------|
| 1 | P40-30 | 40 |
| 2 | P40-45 | 40 |
| 3 | P60-46 | 60 |
| 4 | P60-60 | 60 |
| 5 | P60-80 | 60 |
| 6 | P60-46 TB | 60 |
| 7 | P60-60 TB | 60 |
| 8 | P60-80 TB | 60 |

## Logica di Calcolo

```vb
Select Case iCasing
    Case 1, 2
        PROFILEWIDTH = 40  ' P40 series
    Case 3, 4, 5, 6, 7, 8
        PROFILEWIDTH = 60  ' P60 series
    Case Else
        PROFILEWIDTH = 0   ' Tipo non riconosciuto
End Select
```

## Esempi di Utilizzo

### Esempio 1: Casing P40-30
```vb
' Casing Type: 1 (P40-30)
' Risultato: 40 mm
```

### Esempio 2: Casing P60-60
```vb
' Casing Type: 4 (P60-60)
' Risultato: 60 mm
```

### Esempio 3: Casing P60-80 TB
```vb
' Casing Type: 8 (P60-80 TB)
' Risultato: 60 mm
```

## Note Importanti

### Serie P40
- Profili da 40 mm
- Utilizzati per unità di dimensioni ridotte
- Spessore isolamento: 30 mm o 45 mm

### Serie P60
- Profili da 60 mm
- Utilizzati per unità di dimensioni medie e grandi
- Spessore isolamento: 46 mm, 60 mm o 80 mm

### Thermal Break (TB)
I casing con thermal break mantengono la stessa larghezza profilo della serie corrispondente.

## Applicazioni

Questa macro è utilizzata per:
- Calcolo dimensioni esterne unità
- Calcolo peso profili
- Calcolo costi materiali
- Disegni tecnici e CAD

## Macro Correlate

- **PROFILELEN**: Calcolo lunghezza profili casing
- **OPROFILELEN**: Calcolo lunghezza profili omega
- **OPROFILEQTA**: Calcolo quantità profili omega

## Riferimenti

- **File**: `CBase.vb`
- **Classe**: `CBase`
- **Dipendenze**: Dati generali (GD)
