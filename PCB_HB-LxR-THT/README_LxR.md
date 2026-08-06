# WW-myPCB - HB-LxR-THT – 'AskSin++' Platinen mit bistabilen Relais (THT)

[Zurück zu 'HB-LxR-THT' ...](README.md)

## Spannungsversorgung

Die **HB-LxR-THT Platinen** können wahlweise mit **5 V** oder **7–12 V** betrieben werden.

### 5-V-Betrieb

Bei einer geregelten 5-V-Versorgung wird die Spannung direkt auf die interne **5-V-Schiene** geführt.

### 7–12-V-Betrieb

Bei einer Eingangsspannung von 7–12 V erfolgt die Versorgung über ein **MP1584-Buck-Modul**, das eine stabile 5-V-Versorgung erzeugt.

Die 5-V-Schiene wird mit **100 nF und 47 µF** gepuffert.

Für die Versorgung der Logik und des CC1101 wird ein **MCP1702** eingesetzt, der aus 5 V eine stabile **3,3-V-Versorgung** erzeugt.

## Relaisansteuerung

Jeder Relaiskanal besitzt zwei getrennte Spulen:

* **SET-Spule**
* **RESET-Spule**

Die Ansteuerung erfolgt über je **zwei BS170-NMOS-Transistoren** pro Relais.

### SET

* Pin 1 (+SET) → +5 V
* Pin 16 (−SET) → Drain des BS170

### RESET

* Pin 2 (+RESET) → +5 V
* Pin 15 (−RESET) → Drain des BS170

Die MOSFETs schalten die Minus-Seite der Spulen gegen Masse. Jeder Gate-Eingang besitzt einen **100-Ω-Serienwiderstand** sowie einen **100-kΩ-Pull-Down-Widerstand**.

Zum Schutz der MOSFETs sind an jeder Spule **1N4007-Freilaufdioden** vorgesehen.

Direkt an jedem Relais befindet sich zusätzlich ein **100-nF-Stützkondensator** zwischen +5 V und GND, um Spannungseinbrüche beim Schaltimpuls zu reduzieren.

## Relaiskontakte

Die beiden Umschaltkontakte jedes Relais werden **parallel betrieben**:

* COM1 und COM2 gemeinsam
* NO1 und NO2 gemeinsam

Dadurch reduziert sich der Kontaktübergangswiderstand und die Strombelastbarkeit für **5 V / 2 A** wird verbessert.

Die NC-Kontakte bleiben unbeschaltet.

Zum Schutz gegen transiente Überspannungen an externen Leitungen sind TVS-Dioden vorgesehen:

* **HB-L1R-THT:** D13
* **HB-L2R-THT:** D13 und D23

Die TVS-Dioden müssen entsprechend der verwendeten Betriebsspannung bestückt werden:

| Betriebsspannung | TVS-Diode    |
| ---------------- | ------------ |
| **5 V**          | **P6KE6.8A** |
| **12 V**         | **P6KE15A**  |

Die TVS-Dioden schützen die Relaisausgänge vor Überspannungsspitzen, die beispielsweise durch lange Leitungen oder induktive Lasten entstehen können.

## Konfigurierbare Ausgangsversorgung (J12 / J22)

Jeder Relaiskanal besitzt einen **3-poligen Jumper (J12 bzw. J22)**, mit dem die Versorgung des jeweiligen Relaiskontakts konfiguriert werden kann.

Der Jumper ermöglicht zwei Betriebsarten.

### Stellung OFF (1–2)

In dieser Stellung wird der Relaiskontakt **nicht mit der internen Versorgung verbunden**.

* **VIN_1 (bzw. VIN_2)** steht als **potentialfreier Eingang** zur Verfügung.
* Über das Relais kann somit eine **externe Spannung oder ein externer Stromkreis** geschaltet werden.
* Der Relaiskontakt arbeitet in dieser Betriebsart als **galvanisch getrennter Schaltkontakt**.

### Stellung ON (2–3)

In dieser Stellung wird **VIN_1 (bzw. VIN_2)** mit der internen Versorgung verbunden.

* Bei Betrieb mit **5 V** wird die **5V_RAIL** auf den Relaiskontakt geschaltet.
* Bei Betrieb mit **7–12 V** wird entsprechend die **VIN_12V-Schiene** auf den Relaiskontakt geschaltet.

Der Ausgang **VOUT_1 (bzw. VOUT_2)** führt somit die geschaltete Betriebsspannung des Moduls.

### Zusammenfassung

| Jumperstellung | Funktion                                                                    |
| -------------- | --------------------------------------------------------------------------- |
| **OFF (1–2)**  | Potentialfreier Relaiskontakt (VIN_1 / VIN_2 extern einspeisbar)            |
| **ON (2–3)**   | Durchschalten der internen Versorgung (5 V oder VIN 7–12 V) auf den Ausgang |

Bei der **HB-L1R-THT** ist nur **J12** vorhanden. Die **HB-L2R-THT** besitzt **J12 und J22**.

## Funkmodul

Als Funkmodul wird ein **CC1101** verwendet.

Die Versorgung des CC1101 erfolgt über die 3,3-V-Schiene und ist mit **1 µF und 100 nF** direkt am Modul entkoppelt.

Die CSN-Leitung besitzt einen **10-kΩ-Pull-Up-Widerstand**, sodass das Funkmodul während des Systemstarts zuverlässig deselektiert bleibt.

## Lokale und externe Taster

Jeder Relaiskanal besitzt einen **lokalen Bedientaster** sowie einen **externen Tasteranschluss**.

### Kanal 1

* **SW11** (lokaler Taster)
* **TA11** (externer Tasteranschluss)

Beide sind parallel geschaltet und verbinden **MCU-Pin A1 mit GND**.

### Kanal 2 (nur HB-L2R-THT)

* **SW21** (lokaler Taster)
* **TA21** (externer Tasteranschluss)

Beide sind parallel geschaltet und verbinden **MCU-Pin A2 mit GND**.

Die Eingänge werden in der Firmware mit den internen Pull-Up-Widerständen des ATmega328P betrieben.

## ISP-Programmieranschluss (J6)

Die Module verfügen über einen **6-poligen ISP-Anschluss (J6)** zur direkten Programmierung des **ATmega328P** auf dem Arduino Pro Mini.

Über den ISP-Anschluss können:

* der Bootloader des ATmega328P programmiert,
* die Firmware direkt in den Mikrocontroller übertragen,
* die Fuse-Bits konfiguriert,
* sowie Diagnose- und Testfunktionen durchgeführt werden.

Der Anschluss entspricht der standardmäßigen AVR-ISP-Belegung und ist kompatibel zu gängigen Programmieradaptern (z. B. USBasp, AVRISP mkII oder Atmel-ICE).

### Pinbelegung

| Pin | Signal      |
| --- | ----------- |
| 1   | MISO        |
| 2   | VCC (3,3 V) |
| 3   | SCK         |
| 4   | MOSI        |
| 5   | RESET       |
| 6   | GND         |

Der ISP-Anschluss ist direkt mit den entsprechenden Pins des ATmega328P verbunden. Während der Programmierung wird der Mikrocontroller über die **3,3-V-Versorgung** des Moduls betrieben.

J6 dient ausschließlich der Programmierung und Konfiguration des Mikrocontrollers und ist unabhängig von der seriellen FTDI-Schnittstelle.

## Gemessene Stromaufnahme

Zur Überprüfung der Schaltung wurden beide Modulvarianten als Testaufbau vermessen. Die Messungen erfolgten im betriebsbereiten Zustand einschließlich **Arduino Pro Mini (ATmega328P) mit installierter ASKSIN-Firmware**, **CC1101-Funkmodul** sowie der vollständigen **Spannungsversorgung**.

### HB-L1R-THT (12 V Versorgung)

* **Versorgungsspannung:** 12 V
* **Ruhestrom:** **12 mA**
* **Stromaufnahme während eines Relais-Schaltimpulses:** **16 mA (Peak)**

### HB-L2R-THT (5 V Versorgung)

* **Versorgungsspannung:** 5 V
* **Ruhestrom:** **21 mA**
* **Stromaufnahme während eines Relais-Schaltimpulses:** **34 mA (Peak)**

Die Messwerte zeigen den wesentlichen Vorteil der verwendeten **bistabilen Relais**: Nach dem Schaltvorgang benötigen die Relais keine Halteleistung, sodass die Stromaufnahme im Dauerbetrieb sehr gering bleibt. Die kurzzeitigen Stromspitzen treten ausschließlich während des SET- bzw. RESET-Schaltimpulses auf.

## Besondere Merkmale

* **HB-L1R-THT:** 1 bistabiler Relaiskanal
* **HB-L2R-THT:** 2 bistabile Relaiskanäle
* ausschließlich THT-Bauteile
* Betrieb mit 5 V oder 7–12 V
* hocheffiziente Spannungsversorgung
* galvanische Trennung der Lastkontakte
* lokale und externe Tastereingänge
* ISP-Programmieranschluss
* optimiert für Homematic/ASKSIN

Die Module eignen sich insbesondere für Schaltaufgaben mit geringem Eigenverbrauch, bei denen die Vorteile bistabiler Relais genutzt werden sollen.


## Versionshistorie

### Revision 1.1

Gegenüber **Revision 1.0** wurden folgende Änderungen vorgenommen:

* **Lokale Bedientaster je Kanal hinzugefügt**

  * SW11 für Kanal 1
  * SW21 für Kanal 2 (nur HB-L2R-THT)

* **Externe Tasteranschlüsse ergänzt**

  * TA11 parallel zu SW11
  * TA21 parallel zu SW21 (nur HB-L2R-THT)

* **Entfernung der Einschaltvisualisierung**

  * Die LED-Anzeigen der Relais-Schaltimpulse (J11, D14, R15 sowie J21, D24, R25) wurden entfernt, da sie für den praktischen Betrieb nicht erforderlich sind.
