# OPDIF - Macro per Dimensionamento e Posizionamento Aperture e Serrande

## Panoramica

La macro **OPDIF** è una funzione complessa utilizzata per calcolare le correzioni di dimensionamento e posizionamento per aperture (OP) e serrande (JS/SVK) nelle unità di trattamento aria (UTA). Questa macro gestisce diverse configurazioni di casing, orientamenti delle alette e posizioni delle serrande.

## Sintassi

```vb
OPDIF(iMode As Integer) As String
```

### Parametri

| Parametro | Tipo | Descrizione |
|-----------|------|-------------|
| `iMode` | Integer | Modalità di calcolo (1-4) |

### Valori di Ritorno

Restituisce una stringa nel formato: `"dDifX;dDifY"` dove:
- `dDifX`: Correzione sull'asse X (larghezza) in mm
- `dDifY`: Correzione sull'asse Y (altezza) in mm

## Modalità di Calcolo (iMode)

### Modalità 1: Dimensionamento Aperture (OP)
Calcola le correzioni dimensionali per le aperture in base a:
- Presenza/assenza di serrande
- Tipo di serranda (interna/esterna)
- Orientamento alette (orizzontali/verticali)
- Dimensioni della sezione

### Modalità 2: Posizionamento Aperture (OP)
Calcola le correzioni di posizionamento per le aperture considerando:
- Configurazione serrande
- Dimensioni interne della sezione
- Tipo di casing

### Modalità 3: Dimensionamento Serrande (SC)
Calcola le correzioni dimensionali per le serrande quando:
- Le dimensioni interne superano 1530 mm
- Sono presenti serrande su bocche ventilatori (caso speciale M2K)

### Modalità 4: Posizionamento Serrande (SC)
Calcola le correzioni di posizionamento per le serrande (attualmente non implementato nella versione corrente).

## Variabili Principali

### Dati di Input

| Variabile | Fonte | Descrizione |
|-----------|-------|-------------|
| `OPact` | `O.GETACTID("OP")` | ID dell'apertura corrente |
| `sPos` | `OP.OUT.Classkz` | Posizione apertura (A-K) |
| `iCasing` | `GD.CT.ID` | Tipo di casing (1-8) |
| `dHeight` | `SE.OUT.IntHeight` | Altezza interna sezione |
| `dWidth` | `SE.OUT.IntWidth` | Larghezza interna sezione |
| `m2k` | `SE.OUT.Classkz` | Classe sezione (per M2K) |

### Configurazione Serrande

| Variabile | Tipo | Descrizione |
|-----------|------|-------------|
| `iJs` | Integer | Presenza serranda (0/1) |
| `iInnen` | Boolean | Serranda interna (True/False) |
| `bRot` | Boolean | Alette ruotate (True/False) |
| `iCon` | Integer | Spessore flangia (mm) |

## Posizioni Aperture (sPos)

| Codice | Descrizione |
|--------|-------------|
| A | Destra |
| B | Sinistra |
| C | Sopra |
| D | Sotto |
| E | Frontale completa |
| F | Frontale orizzontale basso |
| G | Frontale orizzontale centrale |
| H | Frontale orizzontale alto |
| I | Frontale verticale sinistra |
| J | Frontale verticale centrale |
| K | Frontale verticale destra |
| L | Bocca ventilatore (M2K) |

## Tipi di Casing

| ID | Descrizione |
|----|-------------|
| 1 | P40-30 |
| 2 | P40-45 |
| 3 | P60-46 |
| 4 | P60-60 |
| 5 | P60-80 |
| 6 | P60-46 TB |
| 7 | P60-60 TB |
| 8 | P60-80 TB |

## Logica di Calcolo

### Modalità 1: Dimensionamento Aperture

#### Caso 1: Solo Apertura (senza serranda)
Per sezioni M2K con posizione C:
```vb
If dWidth <= 2450 Then
    dDifX = (dWidth - (Math.Truncate((dWidth - 240) / 100)) * 100) / 2
Else
    dDifX = (dWidth - (Math.Truncate((dWidth - 480) / 100)) * 100) / 2
End If
```

#### Caso 2: Serranda Esterna con Alette Orizzontali
Per posizioni C, D, H, G, F:
```vb
If dWidth <= 1530 Then
    dDifX = (dWidth - (Math.Truncate(dWidth / 100)) * 100) / 2
End If
```

#### Caso 3: Serranda Esterna con Alette Verticali
Correzioni specifiche per larghezza basate su dimensioni standard:
- 920 mm → dDifX = 5 mm
- 1225 mm → dDifX = 7.5 mm
- 1530 mm → dDifX = 10 mm
- 1840/2140 mm → dDifX = 15 mm
- 2450 mm → dDifX = 20 mm
- Dimensioni maggiori → correzioni progressive

#### Caso 4: Serranda Interna con Alette Orizzontali
```vb
If dWidth <= 2450 Then
    dDifX = (dWidth - (Math.Truncate((dWidth - 240) / 100)) * 100) / 2
Else
    dDifX = (dWidth - (Math.Truncate((dWidth - 480) / 100)) * 100) / 2
End If
```

#### Caso 5: Serranda Interna con Alette Verticali
Correzioni specifiche per larghezza:
- 610/710/810 mm → dDifX = 100 mm
- 920 mm → dDifX = 105 mm
- 1225 mm → dDifX = 107.5 mm
- 1530 mm → dDifX = 60 mm
- 1840/2140 mm → dDifX = 90 mm
- 2450 mm → dDifX = 95 mm
- Dimensioni maggiori → correzioni progressive

### Modalità 2: Posizionamento Aperture

#### Serranda Esterna con Alette Orizzontali
```vb
dDifX = (dWidth - (Math.Truncate(dWidth / 100)) * 100) / 2
```

#### Serranda Interna
Posizionamento fisso basato su configurazione:
- Posizioni H, G, F: `dDifX = 60` o `300` (dipende da larghezza)
- Posizioni C, D: `dDifX = 60`, `dDifY = 60` o `300`
- Posizione E: `dDifX = 180`, `dDifY` variabile

### Modalità 3: Dimensionamento Serrande

#### Caso Speciale: Serrande su Bocche Ventilatori (M2K)
Per sezioni VF con posizione L, correzioni specifiche per marca e diametro ventilatore:

**Comefri (marca = 2):**
| Diametro | dDifX | dDifY |
|----------|-------|-------|
| 180-225 | -70 | -80 |
| 250-315 | -77 | -87 |
| 355-400 | -95 | -105 |
| 450 | -92 | -102 |
| 500 | -92 | -102 |
| 560 | 0 | 0 |
| 630 | -61 | -71 |
| 710 | -98 | -108 |
| 800 | -98 | -108 |
| 900-1000 | -92 | -102 |
| 1120 | -133 | -143 |
| 1250 | -100 | -50 |
| 1400-1600 | -98 | -108 |

**EBM-Papst (marca = 25):**
| Diametro | dDifX | dDifY |
|----------|-------|-------|
| 250 | -135 | -145 |
| 280 | -120 | -130 |
| 315 | -220 | -230 |
| 355-400 | -135 | -145 |
| 450-500 | -155 | -165 |
| 560 | -160 | -170 |

**Ziehl (marca = 9):**
| Diametro | dDifX | dDifY |
|----------|-------|-------|
| 250-280 | -85 | -95 |
| 310 | -170 | -180 |
| 355 | -150 | -160 |
| 400 | -205 | -215 |
| 450 | -150 | -160 |
| 500 | -175 | -185 |
| 560 | -98 | -108 |

#### Caso Generale: Serrande Esterne
Per larghezze > 1530 mm (escluso 2450 mm):
```vb
dDifX = (dWidth - (Math.Truncate(dWidth / 50)) * 50) - 50
```

## Esempi di Utilizzo

### Esempio 1: Apertura Frontale con Serranda Esterna
```vb
' Configurazione:
' - Posizione: E (frontale completa)
' - Larghezza: 920 mm
' - Altezza: 1225 mm
' - Serranda esterna con alette orizzontali

' Chiamata: OPDIF(1) per dimensionamento
' Risultato: "0;7.5"
' - dDifX = 0 (larghezza <= 1530)
' - dDifY = 7.5 (correzione altezza per 1225 mm)
```

### Esempio 2: Apertura Superiore con Serranda Interna M2K
```vb
' Configurazione:
' - Posizione: C (sopra)
' - Larghezza: 1530 mm
' - Sezione M2K
' - Serranda interna con alette orizzontali

' Chiamata: OPDIF(1) per dimensionamento
' Risultato: "60;0"
' - dDifX = 60 (correzione per serranda interna)
' - dDifY = 0 (posizione C)
```

### Esempio 3: Serranda su Bocca Ventilatore
```vb
' Configurazione:
' - Posizione: L (bocca ventilatore)
' - Marca: Comefri (2)
' - Diametro: 500 mm
' - Tipo serranda: JS con ID 18-21

' Chiamata: OPDIF(3) per dimensionamento serranda
' Risultato: "-92;-102"
' - dDifX = -92 (correzione negativa larghezza)
' - dDifY = -102 (correzione negativa altezza)
```

## Note Importanti

### Limitazioni
1. La modalità 4 (posizionamento serrande) non è completamente implementata
2. Le correzioni sono specifiche per i tipi di casing ISC
3. I valori sono calibrati per dimensioni standard di sezioni

### Dipendenze
- Richiede accesso a `O.GetValue()` per leggere parametri sezione
- Utilizza `O.GETACTID()` per identificare componenti attivi
- Dipende dalla corretta configurazione di aperture e serrande

### Considerazioni Speciali
1. **Serrande M2K**: Gestione speciale per serrande su bocche ventilatori con correzioni specifiche per marca
2. **Alette Ruotate**: Logica diversa per alette orizzontali vs verticali
3. **Serrande Interne**: Correzioni maggiori per compensare lo spessore flangia
4. **Dimensioni Non Standard**: Per larghezze > 1530 mm, applicare arrotondamenti a 50 mm

## Versioni Alternative

### OPDIF_BASE
Versione semplificata che gestisce solo i casi base senza le ottimizzazioni M2K.

### OPDIF2021
Versione precedente con logica di calcolo diversa, mantenuta per compatibilità.

### OPDif_origin
Versione originale con logica basata su casing types 1-8 senza supporto M2K.

## Troubleshooting

### Problema: Valori di ritorno "0;0"
**Causa**: Configurazione non riconosciuta o modalità non implementata
**Soluzione**: Verificare che:
- `iMode` sia tra 1 e 3
- La posizione apertura sia valida (A-L)
- Il tipo di casing sia configurato correttamente

### Problema: Dimensioni serranda errate su ventilatori
**Causa**: Marca o diametro ventilatore non riconosciuti
**Soluzione**: 
- Verificare che `marca` sia 2, 9 o 25
- Controllare che il diametro sia nella tabella di lookup
- Verificare che `iDamperID` sia tra 18 e 21 per JS

### Problema: Correzioni non applicate per serrande interne
**Causa**: Flag `iInnen` non impostato correttamente
**Soluzione**: Verificare che `SC.INP.Internal = 1` per serrande interne

## Riferimenti

- **File**: `CBase.vb`
- **Classe**: `CBase`
- **Namespace**: ACN10058
- **Dipendenze**: airCalcMacroWrapper, System.Math

## Changelog

### Versione Corrente (OPDIF)
- Supporto completo per sezioni M2K
- Gestione serrande su bocche ventilatori
- Correzioni specifiche per marca ventilatore
- Logica ottimizzata per alette ruotate

### Versione 2021 (OPDIF2021)
- Logica di calcolo rivista
- Supporto per tutti i tipi di casing
- Gestione migliorata serrande interne

### Versione Base (OPDIF_BASE)
- Implementazione originale
- Supporto base per casing 1-8
- Logica semplificata
