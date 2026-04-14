# KiezRadar

**Prüf in Sekunden, wie sicher dein Fahrrad hier steht.**

KiezRadar ist eine ML-gestützte Consumer-App zur Vorhersage von Fahrraddiebstahlrisiken in Berliner Planungsräumen. Nutzer:innen können einen Ort, eine Uhrzeit, die geplante Abstelldauer, den Fahrradtyp und den ungefähren Wert ihres Fahrrads angeben und erhalten daraus einen **Risikoscore**, eine **verständliche Erklärung** sowie **sicherere Alternativen in der Nähe**.

---

## Idee

KiezRadar beantwortet eine einfache, aber nützliche Frage:

> **Würdest du dein Fahrrad hier jetzt oder heute Abend abstellen?**

Die App kombiniert offizielle Berliner Open-Data-Quellen, Geodaten, Zeitreihen-Features und Machine Learning, um Fahrraddiebstahlrisiken auf räumlich-zeitlicher Ebene zu schätzen.

---

## Ziele des Projekts

- Aufbau einer echten End-to-End-ML-Anwendung
- Demonstration von MLOps-Kompetenz mit reproduzierbaren Pipelines
- Entwicklung eines visuell starken Consumer-Produkts
- Aufbau einer Basis für spätere Erweiterungen wie Alerts, Premium-Funktionen oder eine B2B-API

---

## Problemdefinition

Dieses Projekt versucht **nicht**, den Diebstahl eines konkreten Fahrrads sicher vorherzusagen.

Stattdessen modelliert KiezRadar:

1. das Diebstahlrisiko pro **Planungsraum und Zeitfenster**
2. die **Anpassung an den Nutzerkontext** anhand von Fahrradtyp, Wert und Parkdauer
3. die **Empfehlung sichererer Alternativen** im Umkreis

Dadurch bleibt das System fachlich sauber, realistisch und produktnah.

---

## Zielgruppe

KiezRadar richtet sich zunächst an:

- Berliner Fahrradfahrer:innen
- Pendler:innen
- Studierende
- Menschen, die ihr Fahrrad regelmäßig in unbekannten Gegenden abstellen
- Nutzer:innen, die eine schnelle Risikoabschätzung vor dem Parken wollen

Später kann dieselbe Logik auch für B2B-Produkte genutzt werden, zum Beispiel für Versicherungen, Flotten oder Fahrradinfrastruktur-Anbieter.

---

## Kernfunktionen

### 1. Berlin-Risikokarte
Eine Karte zeigt das geschätzte Fahrraddiebstahlrisiko pro Berliner Planungsraum.

### 2. "Kann ich hier parken?"
Nutzer:innen geben ein:
- Ort
- Datum und Uhrzeit
- Parkdauer
- Fahrradtyp
- Fahrradwert

Die App liefert:
- einen Risikoscore von 0 bis 100
- eine Risikokategorie wie niedrig, mittel oder hoch
- eine kurze verständliche Erklärung

### 3. Sicherere Alternativen
KiezRadar zeigt nahegelegene Alternativen mit geringerem geschätzten Risiko.

### 4. Spätere Erweiterungen
- gespeicherte Orte
- Alerts für Lieblingsorte
- Premium-Features
- B2B-API

---

## Datenquellen

### 1. Fahrraddiebstahl in Berlin
Offizieller Berliner Open-Data-Datensatz zu gemeldeten Fahrraddiebstählen auf Ebene der Planungsräume.

- Datensatzseite: https://daten.berlin.de/datensaetze/fahrraddiebstahl-in-berlin
- Direkte CSV-Ressource: https://www.polizei-berlin.eu/Fahrraddiebstahl/Fahrraddiebstahl.csv

### 2. LOR / Planungsräume
Geometrien der Berliner Planungsräume zur räumlichen Zuordnung und Kartendarstellung.

- https://daten.berlin.de/datensaetze/lebensweltlich-orientierte-raume-lor-01-01-2021-wfs-34c86848

### 3. Radzähldaten in Berlin
Stündliche Fahrradzählungen als Proxy für Exposition und Verkehrsaktivität.

- https://daten.berlin.de/datensaetze?groups=verkehr

### 4. Spätere optionale Erweiterungen
- OpenStreetMap: Fahrradständer, ÖPNV, Points of Interest
- Wetterdaten
- Feiertage / Schulferien
- Nachtleben- und Event-Nähe

---

## Technischer Stack

### Backend
- Python
- FastAPI

### Frontend
- React oder Next.js

### Machine Learning / MLOps
- pandas
- geopandas
- scikit-learn
- LightGBM
- MLflow
- Evidently
- DVC oder lakeFS

### Deployment / Infrastruktur
- Docker
- Kubernetes
- GitLab CI/CD
- Prometheus + Grafana
- Postgres
- Object Storage

---

## Modellierungsansatz

### Baseline
Zuerst wird das Fahrraddiebstahlrisiko auf Ebene von **Planungsraum x Tag** modelliert.

Beispiel-Zielvariable:
- `theft_count per planungsraum per day`

Mögliche erste Modelle:
- Poisson Regression
- Negative Binomial Regression
- LightGBM / XGBoost auf aggregierten Features

### Erste Feature-Gruppen
- Wochentag
- Monat
- Wochenende
- Diebstahlanzahl der letzten 7 Tage
- Diebstahlanzahl der letzten 14 Tage
- Diebstahlanzahl der letzten 30 Tage
- Rolling Mean
- Area ID
- Später: Wetter, OSM, Fahrradzähler, Nachbarschaftsfeatures

### Spätere Erweiterungen
- räumliche Nachbarschaftsfeatures
- Unsicherheitsabschätzung
- Empfehlungssystem für alternative Abstellorte
- Drift Detection
- automatische Retrain-Pipelines

---

## Grobe Architektur

```text
[Open Data Sources]
        |
        v
 [Ingestion Pipelines]
        |
        v
 [Validation + Feature Engineering]
        |
        +------------------+
        |                  |
        v                  v
 [Model Training]     [Batch Scoring]
        |                  |
        v                  v
     [MLflow]         [Risk Tables / API Cache]
        |                  |
        +---------+--------+
                  |
                  v
          [FastAPI Backend]
                  |
                  v
          [Web / Mobile Frontend]
```

---

## Repository-Struktur

```text
kiezradar/
  apps/
    api/
    web/
    training/
  data/
    contracts/
    samples/
  pipelines/
    ingest/
    features/
    train/
    batch_scoring/
  models/
  infra/
    docker/
    k8s/
    terraform/
  monitoring/
  tests/
  docs/
    architecture.md
    data_sources.md
    model_card.md
    runbook.md
  .gitlab-ci.yml
```

---

## MVP

### Version 1
- täglicher Datenimport
- Feature Engineering für Planungsraum x Zeit
- Baseline-Modell für Risikoschätzung
- Berlin-Karte mit Heatmap
- einfacher Score-Endpunkt
- erstes Consumer-UI

### Version 2
- OSM- und Wetter-Enrichment
- bessere Erklärbarkeit
- Vorschläge für sicherere Alternativen
- Monitoring und Drift Detection

### Version 3
- gespeicherte Orte
- Benachrichtigungen
- Premium-Funktionen
- B2B-API

---

## API-Idee

### `POST /score`

Beispiel-Request:

```json
{
  "lat": 52.5200,
  "lon": 13.4050,
  "datetime": "2026-04-16T20:00:00",
  "bike_type": "city_bike",
  "bike_value_eur": 900,
  "duration_minutes": 180
}
```

Beispiel-Response:

```json
{
  "risk_score": 71,
  "risk_label": "hoch",
  "explanations": [
    "In diesem Gebiet gab es zuletzt überdurchschnittlich viele Diebstähle.",
    "Abendstunden sind hier historisch riskanter.",
    "Die lokale Fahrradaktivität ist erhöht."
  ],
  "alternatives": [
    {
      "name": "Alternative 1",
      "distance_m": 180,
      "risk_score": 54
    },
    {
      "name": "Alternative 2",
      "distance_m": 260,
      "risk_score": 49
    }
  ]
}
```

---

## Erste Entwicklungsschritte

1. Repository anlegen
2. Ordnerstruktur erstellen
3. Ingestion-Skript für die CSV bauen
4. LOR-Geometrien einbinden
5. Tagesaggregation pro Planungsraum erstellen
6. Baseline-Features berechnen
7. Erstes Modell trainieren
8. MLflow integrieren
9. Score-API mit FastAPI bauen
10. Ein einfaches Frontend mit Karte und Score-Anzeige erstellen

---

## Beispiel für die ersten Commits

- `feat: initial data ingestion`
- `feat: add lor geometry support`
- `feat: baseline area-day model`
- `feat: add mlflow tracking`
- `feat: add score endpoint`
- `feat: initial heatmap ui`

---

## Warum dieses Projekt spannend ist

KiezRadar verbindet:
- reale urbane Daten
- räumlich-zeitliche ML-Modellierung
- produktnahes Denken
- moderne MLOps-Praktiken
- ein starkes, leicht verständliches Consumer-Use-Case-Design

Das Ergebnis ist nicht nur ein Machine-Learning-Demo-Projekt, sondern eine glaubwürdige Grundlage für ein späteres Produkt.

---

## Hinweis

KiezRadar liefert **Risikoeinschätzungen und Entscheidungshilfen**, aber keine Garantie dafür, ob ein konkreter Diebstahl stattfindet oder nicht.

---

## Status

Projekt in Planung / MVP-Aufbau.