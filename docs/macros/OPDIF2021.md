# OPDIF2021 - Calcolo Dimensionamento Aperture (Versione 2021)

## Panoramica

**OPDIF2021** è una versione aggiornata della macro OPDIF che calcola le correzioni dimensionali per aperture e serrande. Questa versione introduce una logica di calcolo rivista rispetto alla versione base.

## Sintassi

```vb
OPDIF2021(iMode As Integer) As String
```

### Parametri

| Parametro | Tipo | Descrizione |
|-----------|------|-------------|
| `iMode` | Integer | Modalità di calcolo (1-4) |

### Valori di Ritorno

Restituisce una stringa nel formato: `"dDifX;dDifY"`
- `dDifX`: Correzione larghezza in mm
- `dDifY`: Correzione altezza in mm

## Modalità di Calcolo

### Modalità 1: Dimensionamento Aperture
Calcola correzioni dimensionali per aperture considerando:
- Tipo di casing (1-8)
- Presenza e tipo di serranda
- Orientamento alette
- Dimensioni sezione

### Modalità 2: Posizionamento Aperture
Calcola correzioni di posizionamento basate su:
- Configurazione serrande
- Dimensioni interne
- Posizione apertura

### Modalità 3: Dimensionamento Serrande
Calcola correzioni per serrande quando dimensioni > 1530 mm.

### Modalità 4: Posizionamento Serrande
Non implementato nella versione corrente.

## Differenze con OPDIF

### Logica di Calcolo Rivista
- Gestione migliorata per casing types 1-8
- Correzioni ottimizzate per serrande interne
- Supporto esteso per diverse configurazioni

### Compatibilità
Mantenuta per progetti creati con versione 2021 del software.

## Variabili Principali

| Variabile | Fonte | Descrizione |
|-----------|-------|-------------|
| `iCasing` | `GD.CT.ID` | Tipo di casing (1-8) |
| `dHeight` | `SE.OUT.IntHeight` | Altezza interna |
| `dWidth` | `SE.OUT.IntWidth` | Larghezza interna |
| `iJs` | `OP.INP.Damper` | Presenza serranda |
| `iInnen` | `SC.INP.Internal` | Serranda interna |
| `bRot` | `SC.INP.Rotated` | Alette ruotate |

## Esempi di Utilizzo

### Esempio 1: Apertura con Serranda Esterna
```vb
' Larghezza: 1225 mm
' Serranda esterna, alette orizzontali
' Risultato: "7.5;0"
```

### Esempio 2: Apertura con Serranda Interna
```vb
' Larghezza: 1530 mm
' Serranda interna, alette verticali
' Risultato: "60;0"
```

## Note

- Versione precedente mantenuta per compatibilità
- Per nuovi progetti, utilizzare OPDIF
- Logica di calcolo documentata nel codice sorgente

## Riferimenti

- **File**: `CBase.vb`
- **Classe**: `CBase`
- **Versione**: 2021
- **Macro Correlate**: OPDIF, OPDIF_BASE
