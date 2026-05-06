# YAMNet AudioSet Docker Analysis

<div align="center">

![Python](https://img.shields.io/badge/Python-3.9-3776AB?style=for-the-badge&logo=python&logoColor=white)
![TensorFlow](https://img.shields.io/badge/TensorFlow-2.13.0-FF6F00?style=for-the-badge&logo=tensorflow&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-ARM64-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Raspberry Pi](https://img.shields.io/badge/Raspberry%20Pi-5-A22846?style=for-the-badge&logo=raspberrypi&logoColor=white)
![InfluxDB](https://img.shields.io/badge/InfluxDB-2.x-22ADF6?style=for-the-badge&logo=influxdb&logoColor=white)

**Détection automatique de sons dans des fichiers audio et vidéo grâce au modèle IA YAMNet de Google — optimisé pour Raspberry Pi.**

</div>

---

##  Description

Ce projet permet d'analyser des fichiers **audio** (`.wav`) ou **vidéo** (`.mp4`, `.avi`...) afin de détecter automatiquement des événements sonores critiques tels que :

-  Coups de feu / tirs (`Gunshot, gunfire`)
-  Explosions (`Explosion`)
-  Voix / discours (`Speech`)
-  Musique (`Music`)
-  Silence (`Silence`)
-  Autres sons (`Other`)

Le modèle utilisé est **YAMNet** (Yet Another Mobile Network), développé par Google et entraîné sur **521 catégories de sons** du dataset AudioSet. Les résultats sont exportés en **CSV** et envoyés vers **InfluxDB** pour une visualisation en temps réel .

---

##  Architecture du projet

```
yamnet_audioset_analysis_docker/
│
├── Dockerfile                      → Image Docker ARM64 (Raspberry Pi 5)
├── Dockerfile.save                 → Sauvegarde de l'ancienne version
│
├── yamnet_processing.py            → Script principal d'analyse IA
├── requirements_yamnet.txt         → Dépendances Python
│
├──  build.sh                        → Construit l'image Docker
├── ▶️  run.sh                          → Lance l'analyse sur un fichier
├──  demo.sh                         → Démonstration avec fichiers de test
│
├── 🔊 gunshot1.wav                    → Fichier audio de test
└──  gunshot1_gunshot, gunfire.csv   → Résultat d'analyse (exemple)
```



## ⚙️ Seuils de détection

| Catégorie | Seuil de confiance |
|---|:---:|
| 🔇 Silence | 0.50 |
|  Speech | 0.50 |
|  Music | 0.10 |
| Gunshot, gunfire | 0.40 |
| Explosion | 0.40 |
| 🔊 Other | 0.30 |

> Un événement est détecté seulement si le score de YAMNet dépasse le seuil correspondant.

---

## Installation et utilisation

### Prérequis

- Raspberry Pi 5 (ARM64) avec **Raspberry Pi OS 64-bit**
- **Docker** installé sur le Pi
- **InfluxDB 2.x** accessible sur le réseau (container ou serveur)

### 1. Cloner le dépôt

```bash
git clone https://github.com/cheikh646/yamnet__docker_influxdB.git
cd yamnet_docker_influxdB
```

### 2. Construire l'image Docker

```bash
./build.sh
```
> ⏳ Cette étape peut prendre **20 à 40 minutes** sur Raspberry Pi (compilation de TensorFlow ARM64).

### 3. Analyser un fichier audio

```bash
./run.sh mon_audio.wav
```

### 4. Analyser un fichier vidéo

```bash
./run.sh ma_video.mp4
```

### 5. Lancer la démonstration

```bash
./demo.sh
```
> Télécharge automatiquement des fichiers de test et lance l'analyse.

---

##  Format des résultats

### Fichier CSV généré

```
0	960	    Gunshot, gunfire
960	1920	Gunshot, gunfire; Explosion
1920	2880	Silence
```

| Colonne 1 | Colonne 2 | Colonne 3 |
|---|---|---|
| Début (ms) | Fin (ms) | Son(s) détecté(s) |

### Données InfluxDB

Chaque événement envoyé contient :
- `category` → catégorie du son
- `score` → score de confiance (0.0 à 1.0)
- `duration_ms` → durée de l'événement
- `begin_ms` / `end_ms` → timestamps dans l'audio
- `file` → nom du fichier analysé

---



##  Formats de fichiers supportés

| Type | Extensions |
|---|---|
| Audio | `.wav` |
| Vidéo | `.mp4`, `.avi`, `.mpg`, `.mpeg`, `.m4p`, `.m4v`, `.ogg`, `.mpe`, `.mpv` |

---

##  Modèle YAMNet

- **Développeur** : Google / TensorFlow
- **Dataset d'entraînement** : [AudioSet](https://research.google.com/audioset/) (2 millions de clips YouTube)
- **Nombre de catégories** : 521 sons différents
- **Architecture** : MobileNet v1 (optimisé pour l'embarqué)
- **Source** : [github.com/tensorflow/models/audioset/yamnet](https://github.com/tensorflow/models/tree/master/research/audioset/yamnet)

---

##Auteur

<div align="center">

| | |
|---|---|
| **Nom** | Cheikh Brahim Ahmed |
| **Titre** | Élève Ingénieur en Systèmes Embarqués |
| **École** | École Nationale d'Ingénieurs de Tunis (ENIT) |

</div>

---

##  Licence

Ce projet utilise le modèle YAMNet de Google (Apache License 2.0).
