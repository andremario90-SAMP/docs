# PANEL - Calcolo Prezzo/Peso Pannelli

## Panoramica

**PANEL** calcola il prezzo o il peso dei pannelli dell'unità di trattamento aria, considerando lamiera esterna, lamiera interna e isolamento.

## Sintassi

```vb
PANEL(oArg As Object) As String
```

### Parametri

| Parametro | Posizione | Tipo | Descrizione |
|-----------|-----------|------|-------------|
| `sMode` | oArg.Item(1) | String | "PRS" per prezzo, "KG" per peso |
| `iMode` | oArg.Item(2) | Integer | 1=pareti, 2=fondo, 3=parete ventilatore interna |

### Valori di Ritorno

Restituisce una stringa con il valore totale (prezzo in € o peso in kg).

## Logica di Calcolo

### Variabili di Input

| Variabile | Fonte | Descrizione |
|-----------|-------|-------------|
| `iIso` | `GD.CQ.MatID7` | ID materiale isolamento |
| `iPanD` | `GD.CT.Thick` | Spessore pannello (mm) |
| `iMatInt` | `GD.CQ.MatID2/MatID3` | ID materiale lamiera interna |
| `dLamSpesI` | `GD.CQ.Thk2/Thk3` | Spessore lamiera interna (mm) |
| `iMatEst` | `GD.CQ.MatID1` | ID materiale lamiera esterna |
| `dLamSpesE` | `GD.CQ.Thk1` | Spessore lamiera esterna (mm) |

### Componenti Pannello

Un pannello è composto da 3 elementi:

1. **Lamiera Esterna**
   - Materiale: `iMatEst`
   - Spessore: `dLamSpesE`
   - Quantità: 1 m² + bordi (4 lati × spessore pannello)

2. **Lamiera Interna**
   - Materiale: `iMatInt` (diverso per pareti e fondo)
   - Spessore: `dLamSpesI`
   - Quantità: 1 m² + bordi

3. **Isolamento**
   - Materiale: `iIso`
   - Spessore: `iPanD`
   - Quantità: 1 m²

### Formula Calcolo

```vb
' Per ogni componente:
Quantità = 1 + (iPanD / 1000 * 4)  ' 1 m² + bordi

' Lista materiali:
oList.Add({sKeyExt, "Lamiera esterna", Quantità, 3, iMatEst, dLamSpesE * 100})
oList.Add({sKeyInt, "Lamiera interna", Quantità, 3, iMatInt, dLamSpesI * 100})
oList.Add({sKeyIso, "Isolamento", 1, 1, iIso, iPanD})
```

### Fattori di Scarto

| Indice | Percentuale | Applicazione |
|--------|-------------|--------------|
| 0 | 0% | Nessuno scarto |
| 1 | 3% | Scarto minimo |
| 2 | 5% | Scarto standard |
| 3 | 10% | Scarto medio |
| 4 | 30% | Scarto elevato |

Per i pannelli si usa **fattore 3** (10% di scarto).

## Modalità di Calcolo

### Modalità 1: Pareti
- Lamiera interna: `MatID2` / `Thk2`
- Lamiera esterna: `MatID1` / `Thk1`
- Isolamento: `MatID7`

### Modalità 2: Fondo
- Lamiera interna: `MatID3` / `Thk3` (spessore maggiorato)
- Lamiera esterna: `MatID1` / `Thk1`
- Isolamento: `MatID7`

### Modalità 3: Parete Ventilatore Interna
- Lamiera interna = Lamiera esterna (stesso materiale)
- Nessuna lamiera esterna aggiuntiva
- Isolamento: `MatID7`

## Esempi di Utilizzo

### Esempio 1: Calcolo Prezzo Pannello Parete
```vb
' Configurazione:
' - Casing: P60-60 (spessore 60 mm)
' - Lamiera esterna: AISI 304, 0.8 mm
' - Lamiera interna: Zincata, 0.6 mm
' - Isolamento: Lana minerale 50 kg/m³

' Chiamata: PANEL("PRS", 1)
' Calcolo:
' - Lamiera est: (1 + 0.06*4) m² × 0.8 mm × prezzo AISI 304
' - Lamiera int: (1 + 0.06*4) m² × 0.6 mm × prezzo Zincata
' - Isolamento: 1 m² × 60 mm × prezzo lana minerale
' Risultato: "125.50" (esempio)
```

### Esempio 2: Calcolo Peso Pannello Fondo
```vb
' Configurazione:
' - Casing: P60-60 (spessore 60 mm)
' - Lamiera esterna: AISI 304, 0.8 mm (7.85 kg/m²/mm)
' - Lamiera interna fondo: Zincata, 1.0 mm (7.85 kg/m²/mm)
' - Isolamento: Lana minerale (2.5 kg/m²)

' Chiamata: PANEL("KG", 2)
' Calcolo:
' - Lamiera est: 1.24 m² × 0.8 mm × 7.85 = 7.79 kg
' - Lamiera int: 1.24 m² × 1.0 mm × 7.85 = 9.73 kg
' - Isolamento: 1 m² × 2.5 = 2.5 kg
' Risultato: "20.0" kg
```

### Esempio 3: Pannello Parete Ventilatore
```vb
' Configurazione:
' - Casing: P60-60
' - Materiale: AISI 304, 1.0 mm (entrambi i lati)
' - Isolamento: Lana minerale

' Chiamata: PANEL("PRS", 3)
' Calcolo:
' - Lamiera est: (1 + 0.06*4) m² × 1.0 mm × prezzo
' - Lamiera int: (1 + 0.06*4) m² × 1.0 mm × prezzo
' - Isolamento: 1 m² × prezzo
' Risultato: "145.00" (esempio)
```

## Codici Materiale

### Formato Codice
```
SHP-{MaterialCode}      ' Per lamiere
ISO-{MaterialCode}      ' Per isolamento
```

### Materiali Comuni

| ID | Codice | Descrizione | Peso (kg/m²/mm) |
|----|--------|-------------|-----------------|
| 1 | VZ | Lamiera zincata | 7.85 |
| 2 | V2A | AISI 304 | 7.85 |
| 3 | V4A | AISI 316 | 7.85 |
| 4 | ALZN | AluZinc | 2.70 |
| 39 | ALZN | AluZinc | 2.70 |
| 100 | MW50 | Lana minerale 50 kg/m³ | 2.5 |
| 101 | MW80 | Lana minerale 80 kg/m³ | 4.0 |
| 102 | PIR | Poliuretano | 2.0 |

## Note Importanti

### Spessori Standard

**Lamiera Esterna:**
- 0.6 mm: Unità piccole
- 0.8 mm: Standard
- 1.0 mm: Unità grandi o ambienti aggressivi

**Lamiera Interna Pareti:**
- 0.5 mm: Minimo
- 0.6 mm: Standard
- 0.8 mm: Rinforzato

**Lamiera Interna Fondo:**
- 0.8 mm: Minimo
- 1.0 mm: Standard
- 1.2 mm: Rinforzato (unità pesanti)

### Spessori Pannello per Casing

| Casing | Spessore (mm) | Isolamento Tipico |
|--------|---------------|-------------------|
| P40-30 | 30 | Lana minerale |
| P40-45 | 45 | Lana minerale |
| P60-46 | 46 | Lana minerale |
| P60-60 | 60 | Lana minerale / PIR |
| P60-80 | 80 | Lana minerale / PIR |

### Calcolo Bordi

I bordi del pannello aggiungono materiale per la piegatura:
```
Bordi = 4 lati × spessore pannello
Area totale = 1 m² + (4 × spessore / 1000) m²
```

Esempio per pannello 60 mm:
```
Area = 1 + (4 × 0.06) = 1.24 m²
```

## Macro Correlate

- **PANELQM**: Calcolo metri quadri pannelli
- **PANELQMNEW**: Nuovo metodo calcolo pannelli
- **PANCODE**: Generazione codice pannello
- **PANCODENEW**: Nuovo codice pannello
- **PANMANTIME**: Tempo produzione pannelli

## Riferimenti

- **File**: `CPricesAndWeight.vb`
- **Classe**: `CPricesAndWeight`
- **Dipendenze**: Cache materiali, Cache prezzi, Cache pesi
