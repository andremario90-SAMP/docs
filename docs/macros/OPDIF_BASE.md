# OPDIF_BASE - Calcolo Base Dimensionamento Aperture

## Panoramica

**OPDIF_BASE** è la versione semplificata della macro OPDIF che gestisce i casi base di dimensionamento aperture senza le ottimizzazioni specifiche per sezioni M2K.

## Sintassi

```vb
OPDIF_BASE(iMode As Integer) As String
```

### Parametri

| Parametro | Tipo | Descrizione |
|-----------|------|-------------|
| `iMode` | Integer | Modalità di calcolo (1-4) |

### Valori di Ritorno

Restituisce una stringa nel formato: `"dDifX;dDifY"`

## Caratteristiche

### Logica Semplificata
- Calcoli base per tutti i tipi di casing
- Nessuna gestione speciale per M2K
- Correzioni standard per serrande

### Casi Gestiti
1. Aperture senza serrande
2. Serrande esterne con alette orizzontali
3. Serrande esterne con alette verticali
4. Serrande interne con alette orizzontali
5. Serrande interne con alette verticali

## Quando Utilizzare

- Progetti standard senza sezioni M2K
- Configurazioni base di aperture e serrande
- Calcoli semplificati per prototipazione

## Limitazioni

- Non supporta serrande su bocche ventilatori
- Nessuna correzione specifica per marca ventilatore
- Logica base senza ottimizzazioni

## Riferimenti

- **File**: `CBase.vb`
- **Classe**: `CBase`
- **Macro Correlate**: OPDIF, OPDIF2021
