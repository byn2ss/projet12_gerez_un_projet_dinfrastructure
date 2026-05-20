#  SportData POC - Pipeline DataOps d'Animation & Gestion RH

Ce projet présente un pipeline de données industriel (ETL/DataOps) automatisé permettant d'ingérer les flux d'activités sportives des salariés (Simulation de l'API Strava), d'auditer la qualité des données, de calculer des règles de gestion RH (Primes et jours de repos Bien-être), et de générer des publications communautaires pour Slack.

---

##  1. Architecture du Pipeline (Infrastructure & DataOps)

Le pipeline est orchestré de manière moderne afin de garantir la portabilité, le monitoring et la traçabilité des exécutions.

<img width="1920" height="1080" alt="Votre texte de paragraphe" src="https://github.com/user-attachments/assets/f18d4bcf-0935-4601-ad78-865ab4dcaead" />


### Description des Composants :
* **Orchestrateur & Monitoring (Kestra) :** Pilote l'ensemble du workflow, planifie les tâches, gère le cycle de vie du script et fournit des tableaux de bord de suivi (temps d'exécution, statuts succès/échec).
* **Conteneurisation (Docker) :** Assure que Kestra et l'environnement d'exécution (Python, dépendances) tournent de manière isolée et portable.
* **Moteur d'ETL (Python / Pandas) :** Transforme, nettoie, calcule les distances et applique les règles de gestion financières.
* **Bouclier de Qualité (Great Expectations) :** Audit de conformité des données avant écriture en base (Data Quality Gate).
* **Stockage (SQLite) :** Base de données relationnelle locale stockant le reporting RH et le flux d'activités.
* **Restitution (Power BI) :** Export d'un fichier CSV formaté spécifiquement pour l'import automatisé et les indicateurs graphiques.

---

## 🛠️ . Configuration & Orchestration (Fichier YAML Kestra)

Voici la configuration du flux utilisé sur Kestra :
Des triggers on été mis en place pour assurer la véracité des données. Tous les matins à 8:00 les données sont rechargées.

```yaml
id: sport_data_kestra_ok
namespace: company.hr

tasks:
  - id: execution_etl
    type: io.kestra.plugin.scripts.python.Script
    taskRunner:
      type: io.kestra.plugin.core.runner.Process
    beforeCommands:
      - pip install pandas numpy faker openpyxl requests python-dotenv great-expectations
    namespaceFiles:
      enabled: true
    script: |
      #Scrip.py executé ici 

```

---

## 3. Sécurisation & Externalisation (`.env`)

Je travail avec des données RH donc très sensible. Afin de respecter les regles de bonnes pratiques de sécurité, aucun paramètre métier ou variable d'infrastructure est écrit "en dur". Tout est centralisé dans le fichier `.env` :

```ini
FICHIER_RH=Donnees_RH.xlsx
BASE_SQLITE=SportData_POC.db
EXPORT_CSV=Reporting_RH_Final.csv
SEUIL_ACTIVITES_BIEN_ETRE=15
POURCENTAGE_PRIME=0.05
SLACK_WEBHOOK_URL=[https://hooks.slack.com/services/T000/B000/XXXXXX](https://hooks.slack.com/services/T000/B000/XXXXXX)

```

---

##  4. Logique Métier & Règles de Cohérence

### Calcul Spatial (Mock API Google Maps)
<img width="625" height="283" alt="Maps simulation " src="https://github.com/user-attachments/assets/1f876cdc-1493-4fae-93a8-c832b90c4027" />
Le script prend l'adresse textuelle des employés et calcule la distance réelle en kilomètres vers le siège social situé à Lattes (Montpellier : 7.5km, Pérols : 4.2km, etc.).

### Simulation Cohérente du Flux Strava
<img width="618" height="427" alt="Capture d’écran 2026-05-21 005154" src="https://github.com/user-attachments/assets/7b17875c-8da5-4324-b055-59176882353a" />
Pour simuler le temps réel, le script génère un flux de **2 500 événements** parfaitement cohérents avec la demande de Juliette (si un employé déclaré en aller en velo au travil il se verra attribuer des sessions "Vélo"). L'escalade stationnaire force automatiquement la distance à `0.0 km`.

### Audit Data Quality (Great Expectations)


Trois règles de conformité strictes sont appliquées sur le flux généré, elle sont vérifé par Great_expectations :

1. Non-nullité des identifiants salariés.
2. Bornage réaliste des distances (entre 0 et 50 km).
3. Validation des types de sports autorisés.

---
<img width="908" height="433" alt="logs succes " src="https://github.com/user-attachments/assets/0fa181a8-a819-4fb8-bed4-892ad9b46125" />

<img width="917" height="378" alt="Capture d’écran 2026-05-20 002324" src="https://github.com/user-attachments/assets/7ee481b2-aa2d-4c57-bd78-d0f08170d61a" />

## 5 Restitutions & Animation Slack

Une fois le flux audité et validé par Great Expectations, le pipeline génère les messages d'animation communautaires avec conversion des distances en kilomètres, des temps en minutes, et inclusion des émojis et commentaires afin de creer une communauté et donc de générer de la motivation :
<img width="1320" height="284" alt="IMG_3426" src="https://github.com/user-attachments/assets/f8c80371-f9bf-436c-8a45-40ef80bd7612" />

* **Course à pied :** `🤖 [Slack - #club-sport] : "Bravo Audrey Colin ! Tu viens de courir 7.5 km en 42 min ! Quelle énergie ! 🔥🏅"`
* **Vélo :** `🤖 [Slack - #club-sport] : "Superbe session de Vélo pour Bertrand Grondin ! 8.0 km parcourus en 26 min ! Ensemble vers le bien-être ! 🚴‍♂️💨"`
* **Escalade :** `🤖 [Slack - #club-sport] : "Superbe session de Escalade pour Mathilde Dias ! 0.0 km parcourus en 84 min ! Ensemble vers le bien-être ! 🚴‍♂️💨"`

## 7. Restitution Stratégique & Business Intelligence (Power BI)

Le fichier analytique `Reporting_RH_Final.csv` généré à la racine de l'infrastructure par l'orchestrateur Kestra est configuré pour alimenter automatiquement un tableau de bord décisionnel sur **Power BI Desktop**. 

Ce livrable final permet à la direction des Ressources Humaines de piloter en temps réel l'impact de la politique RSE et d'auditer la conformité des dépenses financières.

### Indicateurs Clés (KPIs) & Visuels Intégrés :
* **Pilotage de la Masse Financière (RH) :** Somme dynamique et distribution des montants réels des primes.
* **Audit de Conformité (Data Governance) :** Graphique de répartition des salariés validés comme `Éligible` vs ` Non Éligible` en corrélant le mode de transport déclaré et la distance calculée par le Mock API Google Maps.
* **Score Card Bien-être & Engagement :** Suivi du volume global des 2 500 activités ingérées depuis le flux Strava, et décompte précis des collaborateurs ayant validé le seuil critique des 15 sessions pour débloquer leurs **5 jours de congés bonus**.

### Spécifications Techniques de l'Import :
Pour garantir la robustesse de l'actualisation sans intervention humaine, l'import Power Query respecte les standards de production suivants :
* **Format de fichier :** CSV encodé en `UTF-8-SIG` (conservation parfaite des accents des noms des salariés).
* **Séparateur de colonnes :** Point-virgule (`;`) et format décimal à la virgule (`,`) pour une compatibilité native avec les configurations régionales européennes.

### Aperçu du Dashboard Interactif :
Pensez à insérer une capture d'écran de votre rapport Power BI finalisé pour illustrer cette section :

<img width="525" height="293" alt="powerbi" src="https://github.com/user-attachments/assets/0179ff83-42e8-44a5-a193-9f27cb495c94" />


---

## 6. Déploiement Local

1. Lancer Docker Desktop.
2. Démarrer Kestra en liant le volume de données :

```bash
docker run --pull always -p 8080:8080 -v /var/run/docker.sock:/var/run/docker.sock kestra/kestra:latest server local
```

3. Importer le fichier Excel de données dans l'onglet **Files** de Kestra.
4. Exécuter le Flow depuis l'interface à l'adresse `http://localhost:8080`.

