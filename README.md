### Détection de Crises d'Épilepsie avec Deep Learning - Prétraitement et Segmentation
---

## Table des matières

1. [Préparation des données](#préparation-des-données)
   1.1. [Compréhension du dataset](#compréhension-du-dataset)
   1.2. [Prétraitement des données](#prétraitement-des-données)
2. [Segmentation des données](#segmentation-des-données)
3. [Structure du projet](#structure-du-projet)
4. [Instructions pour l’équipe](#instructions-pour-léquipe)


---

## 1. Préparation des données

### 1.1 Compréhension du dataset

Le dataset utilisé est le **CHB-MIT EEG**, qui a été converti en un fichier CSV. Ce fichier contient des données EEG de patients souffrant de crises d'épilepsie, avec des annotations indiquant si chaque signal est associé à une crise ou non.

**Objectifs de cette étape :**
- Vérification des colonnes du dataset et des valeurs (par exemple, s’il y a des valeurs manquantes).
- Compréhension de la structure du dataset et des annotations des crises.

### 1.2 Prétraitement des données

Dans cette étape, j'ai appliqué des techniques de **normalisation** des signaux EEG, afin d’avoir des données sur une même échelle et de faciliter l’entraînement des modèles. L’objectif était de réduire les biais dus à la variance des signaux.

J’ai également vérifié les **valeurs manquantes** pour m'assurer que les données soient propres avant de passer à la segmentation.

---

## 2. Segmentation des données

### Pourquoi la segmentation ?

La segmentation des signaux EEG est une étape **cruciale** dans le traitement des données EEG. Un signal EEG est une série temporelle continue et il est souvent difficile de l'analyser directement en entier. En découpant les signaux en fenêtres temporelles, on peut extraire des segments plus petits, plus facilement analysables, tout en conservant les informations temporelles.

**Objectifs de la segmentation :**
- Découper les signaux EEG en segments de taille fixe.
- Appliquer un étiquetage des segments en fonction de la présence ou non de crise (étiquetage binaire : crise / non-crise).
- Assurer que chaque segment contienne des données temporelles qui peuvent être utilisées pour entraîner un modèle de Deep Learning.

### Comment fonctionne la segmentation ?

Le code de segmentation lit le fichier CSV par **chunks** de 1000 lignes et découpe chaque chunk en **fenêtres temporelles**. Chaque fenêtre est ensuite étiquetée comme contenant une crise ou non, selon la présence de crises dans la fenêtre.

Les étapes détaillées sont :
1. Lire le fichier CSV en **chunks** (par blocs de 1000 lignes). Cette approche permet de ne pas saturer la RAM, car le fichier complet est trop volumineux pour être chargé entièrement en mémoire.
2. Segmenter chaque chunk en fenêtres temporelles de taille 256 (par exemple, une fenêtre représentant 1 seconde de données EEG si les données sont échantillonnées à 256 Hz).
3. Appliquer un **pas de glissement** de 64, ce qui permet de créer des fenêtres qui se chevauchent.
4. Étiqueter chaque segment selon la présence d’une crise (si une crise est présente dans la fenêtre, le label sera 1 ; sinon, il sera 0).
5. Sauvegarder les segments dans des fichiers `.npz` compressés.

### Pourquoi l'usage des chunks ?

L'utilisation des **chunks** permet de traiter les données par petits blocs, ce qui est particulièrement important pour un fichier de grande taille. Cette approche permet de réduire l’utilisation de la mémoire et d'assurer que l'ensemble du fichier peut être traité sans causer de plantage du système.

---

## 3. Structure du projet

Le projet est structuré comme suit :

```
.
├── preprocessing.ipynb          # Prétraitement des données (normalisation, gestion des valeurs manquantes)
├── segmentation.ipynb           # Segmentation des signaux EEG en fenêtres temporelles
├── requirements.txt             # Dépendances nécessaires pour exécuter le projet
└── README.md                    # Ce fichier
```

---

## 4. Instructions pour l’équipe

Voici les instructions pour que l’équipe puisse reprendre le travail et poursuivre le projet.

### Étape 1 : Installer les dépendances

Dans un terminal ou en environnement virtuel, installez les dépendances nécessaires avec le fichier `requirements.txt`.

```bash
pip install -r requirements.txt
```

### Étape 2 : Prétraitement des données

1. **Normalisation des données :** Ouvrir le fichier `preprocessing.ipynb` pour voir comment les données ont été normalisées.
2. **Vérification des valeurs manquantes :** Ce notebook vérifie également les valeurs manquantes et les gère si nécessaire.

### Étape 3 : Segmentation des données

1. Ouvrir le fichier `segmentation.ipynb` pour segmenter les données EEG.
2. Le code dans ce fichier génère des segments de taille 256 (1 seconde si la fréquence d'échantillonnage est 256 Hz) et les enregistre sous forme de fichiers `.npz` dans le dossier `segments_npz/`.
3. Le script fonctionne par **chunks**, donc vous pouvez l'exécuter sur de grandes quantités de données sans rencontrer de problèmes de mémoire.

### Étape 4 : Entraînement du modèle de Deep Learning

Une fois la segmentation effectuée, l’équipe pourra utiliser ces segments pour entraîner un modèle de Deep Learning (RNN, LSTM, GRU, CNN, etc.).

### Étape 5 : Déploiement et simulation du flux EEG en temps réel

À partir des segments générés, vous pourrez également simuler un flux de données EEG en temps réel (en utilisant Node-RED ou d'autres outils).

---


