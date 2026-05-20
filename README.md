# Gerez_un_projet_dinfrastructure

Mise en place d'un POC Avantages Sportifs pour l'entreprise Sport Data Solution

1. Objectif du Projet
Ce projet est un Proof of Concept (POC) visant à automatiser le système de récompenses sportives pour les salariés de Sport Data Solution. Il permet de :

- Valider la cohérence des déclarations des salariés (trajets domicile-travail).

- Calculer l'impact financier des primes de mobilité (5%).

- Attribuer des jours de repos "Bien-être" basés sur l'activité réelle.

- Automatiser la communication interne via des messages Slack.
<img width="468" height="141" alt="Capture d’écran 2026-03-27 à 14 16 53" src="https://github.com/user-attachments/assets/042f7368-e5a2-4307-900c-a2d0ab6a8d8c" />

2. Architecture de la Solution
Le pipeline suit une logique ETL (Extract, Transform, Load) qui peut être orchestrée par Kestra :

Extraction : Lecture du fichier RH (Donnees_RH.xlsx) et génération de données sportives via Faker ( outils pour générer des données aléatoires). 
<img width="614" height="148" alt="Capture d’écran 2026-03-27 à 14 15 14" src="https://github.com/user-attachments/assets/cf036dee-bf7f-4f7a-89b5-9cd84ee1405c" />

Transformation : Nettoyage, tests de cohérence géographique et calculs des primes.
<img width="595" height="354" alt="Capture d’écran 2026-03-27 à 14 15 47" src="https://github.com/user-attachments/assets/d98bbe40-5525-4620-b560-d26e036c71ff" />

Chargement : Exportation vers une base de données SQLite et des fichiers CSV pour PowerBI.
<img width="652" height="98" alt="Capture d’écran 2026-03-27 à 14 17 29" src="https://github.com/user-attachments/assets/2ba0a53b-caba-444d-8acb-86a8303625e1" />

3. Installation et Utilisation
A. Prérequis
- Python 3.11+
- Pip (gestionnaire de paquets)

B. Configuration de l'environnement
Pour garantir la robustesse et l'isolation du projet :
1.B Création de l'environnement virtuel
python3 -m venv venv

2.B Activation
source venv/bin/activate  # Sur Mac/Linux

3.B Installation des dépendances
pip install -r requirements.txt

C. Exécution du pipeline
Commande : python Script.py
Tests de Cohérence et Qualité (Data Quality)
Le projet intègre des barrières de sécurité pour éviter les erreurs de déclaration :
- Marche/Running : Distance maximale autorisée de 15 km.

- Vélo/Trottinette : Distance maximale autorisée de 25 km.
<img width="682" height="495" alt="Capture d’écran 2026-03-27 à 14 18 28" src="https://github.com/user-attachments/assets/b7ddf651-0acc-4c7d-b4a6-be976b98ca08" />

- Validation SQL : Nettoyage automatique des colonnes dupliquées avant l'injection en base.
<img width="637" height="365" alt="Capture d’écran 2026-03-27 à 14 14 32" src="https://github.com/user-attachments/assets/d5b96a91-08f0-4dfb-8a01-da1bbdde6697" />

Monitoring et Restitution

- Ordonnancement : Le flux est piloté par Kestra pour surveiller l'état d'exécution et la volumétrie.
- Visualisation : Un rapport PowerBI est connecté à la base SQLite pour suivre les KPI financiers en temps réel.

📂 Structure des fichiers
.
├── Donnees_RH.xlsx          # Source de données RH

├── Script.py                # Pipeline Python principal

├── requirements.txt         # Liste des dépendances

├── SportData_POC.db         # Base de données SQLite générée

└── Resultats_RH_Final.csv   # Export pour PowerBI
