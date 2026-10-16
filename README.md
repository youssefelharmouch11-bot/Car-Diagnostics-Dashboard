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

## 📌 Aperçu du Projet
Ce projet consiste à concevoir et prototyper un système embarqué intelligent pour la télémesure et le suivi de paramètres physiques et électriques (température, humidité, tension de batterie), combinant la technologie IoT et les fondamentaux du diagnostic automobile via le protocole standardisé **UDS (ISO 14229)**.

---

## 🧠 Principe du Protocole UDS (ISO 14229)

Le protocole **UDS (Unified Diagnostic Services)**, normalisé par l'**ISO 14229**, est le standard international utilisé dans l'industrie automobile pour le diagnostic électronique des calculateurs (ECU).

### 🔄 Modèle Client-Serveur
Le fonctionnement repose sur une architecture **Client-Serveur** :
- **Le Client (Tester) :** L'outil de diagnostic ou l'interface web qui envoie des requêtes.
- **Le Serveur (ECU / ESP32) :** Le calculateur embarqué qui traite les requêtes, renvoie les données ou effectue les actions demandées.

### 📬 Structure des Trames de Diagnostic

1. **Requête (Request Frame) :**  
   Chaque service est identifié par un **SID (Service Identifier)** sous forme d'un octet hexadécimal (ex: `0x19` pour la lecture des codes défauts) suivi de sous-fonctions ou paramètres.
2. **Réponse Positive (Positive Response Frame) :**  
   Formatée sous la forme **RSID = SID + `0x40`** (ex: pour `0x19`, la réponse commence par `0x59`).
3. **Réponse Négative (Negative Response - NRC) :**  
   Formatée sous la forme `0x7F [SID Demande] [Code NRC]` en cas d'erreur ou d'accès refusé.

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

## 🛠️ Technologies & Outils

| Domaine | Technologies & Outils Utilisés |
| :--- | :--- |
| **Hardware & Embarqué** | ESP32, Capteur DHT11, Diviseur de tension |
| **Langages & Firmware** | C/C++ (Arduino IDE), Python 3 (Flask) |
| **Frontend & Web** | HTML5, CSS3, JavaScript |
| **Normes & Protocoles** | Wi-Fi, HTTP POST, UDS (ISO 14229), API Ntfy |
| **Environnement** | VS Code, Arduino IDE, Git / GitHub |

---

## ⚙️ Étapes de Réalisation
1. **Analyse & Conception :** Modélisation de l'architecture du système embarqué et des flux de données.
2. **Développement Embarqué :** Programmation de l'ESP32 en C/C++ pour la lecture des capteurs et la communication sans fil.
3. **Mise en Place du Backend :** Développement du serveur Flask pour la réception, le traitement des trames et la gestion des services UDS.
4. **Interface Web & Tests :** Création du dashboard interactif et validation de la chaîne complète de diagnostic.

---

## 📊 Résultats Obtenus
- Remontée fiable et en temps réel des données capteurs et de la tension batterie.
- Interface de supervision opérationnelle et ergonomique avec authentification sécurisée.
- Validation expérimentale de la chaîne IoT et de l'émulation du protocole UDS ISO 14229.

---

<p align="center">
  <b>Développé par Youssef El Harmouch</b><br>
  <i>Étudiant Ingénieur en Systèmes Embarqués et Robotique</i>
</p>
