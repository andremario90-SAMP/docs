# CADEXP - Esportazione Dati Geometrici CAD

## Panoramica

**CADEXP** esporta i dati geometrici dell'unità di trattamento aria in formato JSON per l'integrazione con sistemi CAD esterni.

## Sintassi

```vb
CADEXP() As String
```

### Parametri

Nessun parametro richiesto. La macro raccoglie automaticamente tutti i dati geometrici dell'unità corrente.

### Valori di Ritorno

Restituisce una stringa JSON contenente tutti i dati geometrici dell'unità.

## Struttura Dati JSON

### Dati Generali Unità

```json
{
  "ProjectNumber": "string",
  "UnitNumber": "string",
  "UnitDescription": "string",
  "CasingType": integer,
  "CasingDescription": "string",
  "TotalLength": double,
  "TotalWidth": double,
  "TotalHeight": double,
  "TotalWeight": double,
  "Sections": [...]
}
```

### Dati Sezione

```json
{
  "SectionID": integer,
  "SectionType": "string",
  "SectionDescription": "string",
  "Position": integer,
  "Length": double,
  "IntWidth": double,
  "IntHeight": double,
  "ExtWidth": double,
  "ExtHeight": double,
  "Weight": double,
  "Components": [...]
}
```

### Dati Componente

```json
{
  "ComponentID": integer,
  "ComponentType": "string",
  "ComponentDescription": "string",
  "Width": double,
  "Height": double,
  "Depth": double,
  "PosX": double,
  "PosY": double,
  "PosZ": double,
  "Rotation": double,
  "Material": "string",
  "Weight": double
}
```

## Componenti Esportati

### Componenti Strutturali
- Pannelli (superiore, inferiore, laterali)
- Profili casing
- Profili omega
- Basamento
- Piedini

### Componenti Funzionali
- Batterie (riscaldamento, raffreddamento)
- Ventilatori (mandata, ripresa)
- Filtri (varie classi)
- Serrande (regolazione, tagliafuoco)
- Silenziatori
- Recuperatori di calore
- Umidificatori

### Componenti Accessori
- Portelli di ispezione
- Griglie di protezione
- Cappucci di aspirazione/mandata
- Supporti e guide
- Connessioni elettriche
- Connessioni idrauliche

## Esempi di Utilizzo

### Esempio 1: Unità Semplice
```json
{
  "ProjectNumber": "2024-001",
  "UnitNumber": "UTA-01",
  "UnitDescription": "Unità trattamento aria uffici",
  "CasingType": 4,
  "CasingDescription": "P60-60",
  "TotalLength": 4500,
  "TotalWidth": 1590,
  "TotalHeight": 1980,
  "TotalWeight": 850,
  "Sections": [
    {
      "SectionID": 1,
      "SectionType": "FI",
      "SectionDescription": "Filtri",
      "Position": 1,
      "Length": 600,
      "IntWidth": 1530,
      "IntHeight": 1530,
      "ExtWidth": 1590,
      "ExtHeight": 1590,
      "Weight": 120,
      "Components": [
        {
          "ComponentID": 1,
          "ComponentType": "Filter",
          "ComponentDescription": "Filtro G4",
          "Width": 592,
          "Height": 592,
          "Depth": 48,
          "PosX": 0,
          "PosY": 0,
          "PosZ": 0,
          "Rotation": 0,
          "Material": "Synthetic",
          "Weight": 2.5
        }
      ]
    }
  ]
}
```

### Esempio 2: Sezione con Batteria
```json
{
  "SectionID": 2,
  "SectionType": "HW",
  "SectionDescription": "Batteria acqua calda",
  "Position": 2,
  "Length": 800,
  "IntWidth": 1530,
  "IntHeight": 1530,
  "Weight": 180,
  "Components": [
    {
      "ComponentID": 10,
      "ComponentType": "Coil",
      "ComponentDescription": "Batteria 4 ranghi",
      "Width": 1470,
      "Height": 1470,
      "Depth": 400,
      "PosX": 30,
      "PosY": 30,
      "PosZ": 200,
      "Rotation": 0,
      "Material": "Copper-Aluminum",
      "Weight": 85
    }
  ]
}
```

## Sistema di Coordinate

### Origine
L'origine del sistema di coordinate (0,0,0) è posizionata:
- **X**: Angolo anteriore sinistro dell'unità
- **Y**: Base dell'unità
- **Z**: Lato sinistro dell'unità

### Assi
- **Asse X**: Lunghezza (direzione flusso aria)
- **Asse Y**: Altezza (verticale)
- **Asse Z**: Larghezza (orizzontale)

### Unità di Misura
Tutte le dimensioni sono espresse in millimetri (mm).

```
        Y (Altezza)
        │
        │
        │
        └─────────── X (Lunghezza)
       ╱
      ╱
     Z (Larghezza)
```

## Formato Esportazione

### Encoding
- **Charset**: UTF-8
- **Format**: JSON
- **Indentation**: 2 spaces

### Precisione Numerica
- **Dimensioni**: 1 decimale (mm)
- **Pesi**: 2 decimali (kg)
- **Angoli**: 1 decimale (gradi)

## Integrazione CAD

### Software Supportati
- AutoCAD (via script)
- SolidWorks (via API)
- Revit (via plugin)
- SketchUp (via Ruby script)

### Workflow Tipico
1. Eseguire macro CADEXP in airCalc++
2. Salvare output JSON in file
3. Importare file in software CAD
4. Generare modello 3D automaticamente
5. Applicare materiali e rendering

## Note Importanti

### Prestazioni
- L'esportazione può richiedere tempo per unità complesse
- Dimensione file JSON proporzionale alla complessità unità
- Considerare compressione per unità molto grandi

### Precisione
- Le dimensioni sono arrotondate al millimetro
- Le posizioni sono calcolate dal centro componente
- Le rotazioni sono espresse in gradi (0-360)

### Limitazioni
- Non include dati elettrici dettagliati
- Non include dati idraulici dettagliati
- Non include specifiche prestazionali

## Applicazioni

Questa macro è utilizzata per:
- Generazione automatica modelli 3D
- Integrazione con BIM (Building Information Modeling)
- Verifica interferenze in sala macchine
- Generazione layout di installazione
- Documentazione tecnica illustrata
- Presentazioni commerciali

## Macro Correlate

- **TOTALHEIGHT**: Calcolo altezza totale
- **CoilHeight**: Calcolo altezza batteria
- **CoilWidth**: Calcolo larghezza batteria
- **PROFILELEN**: Calcolo lunghezza profili

## Riferimenti

- **File**: `CBase.vb`
- **Classe**: `CBase`
- **Dipendenze**: Newtonsoft.Json, Dati unità completi
- **Standard**: JSON RFC 7159
