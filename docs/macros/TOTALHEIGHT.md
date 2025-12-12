# TOTALHEIGHT - Calcolo Altezza Totale Unità

## Panoramica

**TOTALHEIGHT** calcola l'altezza totale dell'unità di trattamento aria includendo l'altezza della sezione, il basamento e i piedini di supporto.

## Sintassi

```vb
TOTALHEIGHT() As Double
```

### Parametri

Nessun parametro richiesto. La macro legge automaticamente i dati dalla sezione e dall'unità corrente.

### Valori di Ritorno

Restituisce un valore `Double` che rappresenta l'altezza totale dell'unità in millimetri (mm).

## Logica di Calcolo

### Variabili di Input

| Variabile | Fonte | Descrizione |
|-----------|-------|-------------|
| `dHeight` | `SE.OUT.ExtHeight` | Altezza esterna sezione (mm) |
| `iBaseFrame` | `UN.INP.BaseFrame` | Presenza basamento (0/1) |
| `iFoot` | `UN.INP.Foot` | Tipo di piedino |
| `dBaseHeight` | `UN.INP.BaseHeight` | Altezza basamento (mm) |

### Formula Base

```vb
TOTALHEIGHT = dHeight + BaseFrameHeight + FootHeight
```

### Calcolo Altezza Basamento

```vb
If iBaseFrame = 1 Then
    BaseFrameHeight = dBaseHeight
Else
    BaseFrameHeight = 0
End If
```

### Calcolo Altezza Piedini

| Tipo Piedino | Altezza (mm) | Descrizione |
|--------------|--------------|-------------|
| 0 | 0 | Nessun piedino |
| 1 | 50 | Piedino standard |
| 2 | 100 | Piedino regolabile |
| 3 | 150 | Piedino alto |
| 4 | 200 | Piedino molto alto |

```vb
Select Case iFoot
    Case 0
        FootHeight = 0
    Case 1
        FootHeight = 50
    Case 2
        FootHeight = 100
    Case 3
        FootHeight = 150
    Case 4
        FootHeight = 200
    Case Else
        FootHeight = 0
End Select
```

## Esempi di Utilizzo

### Esempio 1: Unità Senza Basamento e Piedini
```vb
' Configurazione:
' - Altezza sezione: 1225 mm
' - Basamento: No (0)
' - Piedini: No (0)

' Calcolo:
' TOTALHEIGHT = 1225 + 0 + 0 = 1225 mm
```

### Esempio 2: Unità con Basamento
```vb
' Configurazione:
' - Altezza sezione: 1225 mm
' - Basamento: Si (1)
' - Altezza basamento: 300 mm
' - Piedini: No (0)

' Calcolo:
' TOTALHEIGHT = 1225 + 300 + 0 = 1525 mm
```

### Esempio 3: Unità con Basamento e Piedini Standard
```vb
' Configurazione:
' - Altezza sezione: 1530 mm
' - Basamento: Si (1)
' - Altezza basamento: 400 mm
' - Piedini: Standard (1)

' Calcolo:
' TOTALHEIGHT = 1530 + 400 + 50 = 1980 mm
```

### Esempio 4: Unità con Piedini Regolabili
```vb
' Configurazione:
' - Altezza sezione: 1840 mm
' - Basamento: Si (1)
' - Altezza basamento: 500 mm
' - Piedini: Regolabili (2)

' Calcolo:
' TOTALHEIGHT = 1840 + 500 + 100 = 2440 mm
```

### Esempio 5: Unità Completa con Piedini Alti
```vb
' Configurazione:
' - Altezza sezione: 2450 mm
' - Basamento: Si (1)
' - Altezza basamento: 600 mm
' - Piedini: Alti (3)

' Calcolo:
' TOTALHEIGHT = 2450 + 600 + 150 = 3200 mm
```

## Componenti dell'Altezza Totale

```
┌─────────────────────────────────┐
│                                 │
│      SEZIONE UTA                │
│   (Altezza Esterna)             │
│                                 │
├─────────────────────────────────┤
│                                 │
│      BASAMENTO                  │
│   (Se presente)                 │
│                                 │
├─────────────────────────────────┤
│                                 │
│      PIEDINI                    │
│   (Se presenti)                 │
│                                 │
└─────────────────────────────────┘
```

## Tabella Riepilogativa Piedini

| Tipo | Codice | Altezza (mm) | Applicazione |
|------|--------|--------------|--------------|
| Nessuno | 0 | 0 | Installazione a pavimento |
| Standard | 1 | 50 | Installazione standard |
| Regolabile | 2 | 100 | Livellamento su pavimenti irregolari |
| Alto | 3 | 150 | Spazio per tubazioni sotto l'unità |
| Molto Alto | 4 | 200 | Installazione sopraelevata |

## Note Importanti

### Unità di Misura
- Input: millimetri (mm)
- Output: millimetri (mm)

### Altezza Basamento
L'altezza del basamento è configurabile e dipende da:
- Spazio necessario per collegamenti
- Altezza tubazioni
- Requisiti di installazione
- Accessibilità per manutenzione

### Considerazioni di Progetto
1. **Altezza Locale**: Verificare che l'altezza totale sia compatibile con l'altezza del locale
2. **Trasporto**: Considerare limiti di altezza per trasporto e movimentazione
3. **Manutenzione**: Garantire spazio sufficiente sopra l'unità
4. **Piedini Regolabili**: Permettono compensazione dislivelli fino a ±20 mm

### Peso e Stabilità
- Piedini più alti richiedono maggiore attenzione alla stabilità
- Verificare portata piedini in base al peso totale unità
- Considerare carichi dinamici (vibrazioni ventilatori)

## Applicazioni

Questa macro è utilizzata per:
- Verifica compatibilità con altezza locale
- Calcolo ingombri per layout sala macchine
- Generazione disegni di installazione
- Calcolo baricentro unità
- Pianificazione trasporto e movimentazione

## Macro Correlate

- **BASEFRAME**: Calcolo componenti basamento
- **FOOT**: Calcolo specifiche piedini
- **CoilHeight**: Calcolo altezza batteria
- **CoilWidth**: Calcolo larghezza batteria

## Riferimenti

- **File**: `CBase.vb`
- **Classe**: `CBase`
- **Dipendenze**: Dati sezione (SE), Dati unità (UN), Dati generali (GD)
