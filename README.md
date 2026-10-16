# 🚗 CarDiagnostics-Dashboard

> **Prototype de tableau de bord de diagnostic automobile connecté (ESP32, Flask, UDS ISO 14229)**  
> *Développé par Youssef El Harmouch dans le cadre du Master en Systèmes Embarqués et Robotique (FST Al Hoceima).*

---

## 📸 Aperçu Visuel du Projet

### 1. Prototype Hardware & Notifications Mobile
![Interface](interface.jpg)

### 2. Écran de Connexion Sécurisé
![Connexion](login_preview.jpg.png)

### 3. Suivi Synthétique du Véhicule
![Vue Véhicule](Vehicule%20.png)

### 4. Tableau de Bord Telemetry & Graphiques Temps Réel
![Dashboard](Dashboard.png)

### 5. Console de Diagnostic UDS
![UDS](UDS.png)

---

## 📌 Aperçu du Projet / Overview
Ce projet consiste à concevoir et prototyper un système embarqué intelligent pour la télémesure et le suivi de paramètres physiques et électriques (température, humidité, tension de batterie), combinant la technologie IoT et les fondamentaux du diagnostic automobile via le protocole standardisé **UDS (ISO 14229)**.

It uses an **ESP32** microcontroller to acquire sensor data and transmit it to a **Python Flask** backend over HTTP POST. The system integrates real-time telemetry gauges, SQLite database logging, instant mobile notifications via **Ntfy**, and a dedicated web-based diagnostic console emulating standard automotive UDS frames.

---

## 🧠 Principe du Protocole UDS (ISO 14229)

Le protocole **UDS (Unified Diagnostic Services)**, normalisé par l'**ISO 14229**, est le standard international utilisé dans l'industrie automobile pour le diagnostic électronique des calculateurs (ECU).

### 🔄 Modèle Client-Serveur
Le fonctionnement repose sur une architecture **Client-Serveur** :
- **Le Client (Tester) :** L'outil de diagnostic ou l'interface web qui envoie des requêtes.
- **Le Serveur (ECU / ESP32) :** Le calculateur embarqué qui traite les requêtes, renvoie les données ou effectue les actions demandées.

### 📬 Structure des Trames de Diagnostic
1. **Requête (Request Frame) :** Chaque service est identifié par un **SID (Service Identifier)** en hexadécimal (ex: `0x19` pour la lecture des DTCs) suivi de sous-fonctions ou paramètres.
2. **Réponse Positive (Positive Response Frame) :** Formatée sous la forme **RSID = SID + `0x40`** (ex: pour `0x19`, la réponse commence par `0x59`).
3. **Réponse Négative (Negative Response - NRC) :** Formatée sous la forme `0x7F [SID Demande] [Code NRC]` en cas d'erreur ou d'accès refusé.

### 🛠️ Services UDS Implémentés dans ce Projet

| SID | Service UDS | Description & Rôle dans le Projet | Exemple de Trame |
| :---: | :--- | :--- | :--- |
| **`0x10`** | **DiagnosticSessionControl** | Commutation entre session par défaut, étendue (*Extended*) et de programmation. | Requête : `10 03` <br> Réponse : `50 03 ...` |
| **`0x27`** | **SecurityAccess** | Déverrouillage des fonctions sécurisées via échange de clé/graine (*Seed & Key*). | Requête : `27 01` <br> Réponse : `67 01 [Seed]` |
| **`0x19`** | **ReadDTCInformation** | Lecture des codes défauts (*Diagnostic Trouble Codes* - DTC) enregistrés. | Requête : `19 02 FF` <br> Réponse : `59 02 09 ...` |
| **`0x22`** | **ReadDataByIdentifier** | Lecture des données temps réel identifiées par un DID (VIN, tension batterie). | Requête : `22 F1 90` *(Read VIN)* <br> Réponse : `62 F1 90 [VIN]` |
| **`0x14`** | **ClearDiagnosticInformation** | Effacement des codes défauts (DTC) enregistrés dans le système. | Requête : `14 FF FF FF` <br> Réponse : `54` |

---

## 🚀 Fonctionnalités Clés
- **ESP32 & DHT11 :** Collecte des données environnementales et de la tension batterie, puis transmission via HTTP POST vers le serveur Flask.
- **Serveur Flask :** Traitement des données, journalisation SQLite et gestion de l'interface web de supervision.
- **Tableau de Bord Web :** Interface interactive, ergonomique et sécurisée pour la visualisation en temps réel.
- **Diagnostics UDS (ISO 14229) :** Émulation complète des trames et services de diagnostic automobile.
- **Alertes Push Mobile :** Notification en temps réel sur smartphone via l'API Ntfy en cas de dépassement de seuils critiques.

---

## 🛠️ Requirements & System Setup

### Hardware Requirements
- **ESP32** development board
- **DHT11** temperature & humidity sensor
- **Voltage Divider Network** (for battery monitoring)
- **4.7kΩ to 10kΩ pull-up resistor**
- **Breadboard & Jumper Wires**
- **USB Cable** for ESP32 programming & power

### Software Requirements
- **Arduino IDE** (with ESP32 board support)
- **Python 3.x**
- **Flask** (`pip install flask`)
- **Flask-CORS** (`pip install flask-cors`)
- **Requests** (`pip install requests`)
- **SQLite3**

---

## ⚙️ Setup Instructions

### 1. ESP32 Firmware Setup
1. Open **Arduino IDE**, navigate to **File > Preferences**, and add `https://dl.espressif.com/dl/package_esp32_index.json` to **Additional Boards Manager URLs**.
2. Go to **Tools > Board > Boards Manager**, search for **ESP32**, and install the board package.
3. Install the **DHT sensor library** by Adafruit via **Sketch > Include Library > Manage Libraries**.
4. **Hardware Connections:**
   - **VCC (DHT11)** → 3.3V / 5V
   - **GND (DHT11)** → GND
   - **DATA (DHT11)** → GPIO 4 (with a 4.7kΩ–10kΩ pull-up resistor to VCC)
   - **Battery Analog Pin** → GPIO 34 (via voltage divider)
5. Open `esp32_car_monitor.ino`, update your Wi-Fi credentials and server URL:
   ```cpp
   const char* ssid = "YOUR_WIFI_SSID";
   const char* password = "YOUR_WIFI_PASSWORD";
   const char* serverName = "[http://192.168.1.](http://192.168.1.)X:5000/api/update";
