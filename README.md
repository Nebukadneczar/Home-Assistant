# Home-Assistant

Sammlung von Home-Assistant-Blueprints und Automationen für Energie-, Batterie-, Solar- und Miner-Steuerung.

## Antminer – Batterie-/Solarsteuerung nach Ladeleistung

**Version: v1.1.0**

Blueprint:
`blueprints/automation/antminer/antminer_batterie_solar.yaml`

Seit v1.1.0 ist die **tatsächliche Batterieladeleistung die primäre Regelgröße**. Der Miner wird dynamisch so geregelt, dass nach der Anpassung eine konfigurierbare Lade-Reserve in der Batterie verbleibt.

### Neue Regelung

Die Berechnung verwendet den aktuellen Miner-Zielwert mit:

```
neues Miner-Ziel =
    aktuelle Ladeleistung
    + aktuelles Miner-Ziel
    - Lade-Reserve
```

Das Ergebnis wird auf die konfigurierte maximale Minerleistung begrenzt.

Damit wird der aktuelle Minerverbrauch bei der Berechnung berücksichtigt. Beispiel:

```
Batterie lädt:       1.860 W
Miner aktuell:         944 W
Reserve:               200 W

1.860 + 944 - 200 = 2.604 W
→ Miner-Ziel: 2.604 W
```

Nach der Erhöhung des Miners sinkt die Batterieladeleistung entsprechend. Bei der nächsten Regelprüfung wird erneut gerechnet. Dadurch kann sich die Steuerung auf einen stabilen Betriebspunkt einregeln, anstatt nur feste SOC-Stufen zu schalten.

Bei einer Batterieladeleistung von **0 W oder weniger** wird der Miner gestoppt.

### Beispiel mit deinem aktuellen System

Bei einer Anzeige wie:

- PV Power Gesamt: **2.793 W**
- Batterieladeleistung: **1.860 W**

ist nicht automatisch die gesamte PV-Leistung für den Miner verfügbar. Die Batterieladeleistung zeigt bereits, was nach den übrigen Verbrauchern aktuell noch in die Batterie geht.

Bei **1.860 W Ladeleistung** und einem laufenden Miner mit **944 W** sowie **200 W Reserve** ergibt sich zunächst ein neues Ziel von **2.604 W**. Das liegt unter der maximalen Minerleistung von 2.832 W.

### Eigenschaften

- Ladeleistung statt SOC-Kennlinie als primäre Regelgröße
- dynamische Leistungsberechnung
- konfigurierbare Lade-Reserve
- konfigurierbare maximale Minerleistung
- konfigurierbare Mindeständerung
- bei fehlender Ladeleistung: Miner STOP
- SOC bleibt für Statusanzeige und Benachrichtigungen erhalten
- 1/5/10/15-Minuten-Prüfintervall
- 30-Sekunden-Wartezeit nach Home-Assistant-Start
- unterstützt positive oder negative Vorzeichen der Batterieleistung

### Eingaben

| Eingabe | Funktion |
|---|---|
| **Batterie SOC** | Anzeige des Ladezustands |
| **Batterie-Leistung** | Aktuelle Batterieleistung |
| **Vorzeichen bei Batterieladung** | Positiv oder negativ |
| **Miner Leistungsziel** | `number.`-Entität des Miners |
| **Miner Start** | Start-Button |
| **Miner Stop** | Stop-Button |
| **Maximale Minerleistung** | Absolute Obergrenze |
| **Lade-Reserve** | Gewünschte Rest-Ladeleistung der Batterie |
| **Mindeständerung** | Verhindert kleine unnötige Stellbefehle |
| **Batteriekapazität** | kWh-Anzeige |
| **Prüfintervall** | Regelzyklus |
| **Benachrichtigungs-ID** | Persistente Meldung |

### Beispielkonfiguration für das getestete System

```
sensor.growatt_battery_battery_soc
sensor.growatt_battery_battery_power
number.antminer36_power_target
button.solargarten_antminer36_bosminer_start
button.solargarten_antminer36_bosminer_stop
```

Gesamtkapazität: **25 kWh**

Bei diesem System gilt:

- **positiver Wert der Batterieleistung = Laden**
- **negativer Wert = Entladen**

### Installation

**Import-URL:**

https://raw.githubusercontent.com/Nebukadneczar/Home-Assistant/main/blueprints/automation/antminer/antminer_batterie_solar.yaml

In Home Assistant:

**Einstellungen → Automatisierungen & Szenen → Blueprints → Blueprint importieren**

Die Blueprint-Version wurde von **v1.0.1 auf v1.1.0** umgestellt. Die frühere SOC-Kennlinie ist damit nicht mehr die aktive Leistungsregelung.

## Repository-Struktur

```
Home-Assistant/
├── README.md
├── LICENSE
├── docs/
│   └── images/
│       └── antminer-batterie-solar-demo.svg
└── blueprints/
    └── automation/
        └── antminer/
            └── antminer_batterie_solar.yaml
```
