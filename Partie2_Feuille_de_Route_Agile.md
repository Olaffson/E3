# PARTIE 2 : FEUILLE DE ROUTE AGILE
## Projet Education Data Hub - Planification des Sprints

---

## 2.1 PRODUCT BACKLOG PRIORISÉ

### 2.1.1 Vue d'Ensemble du Product Backlog

Le Product Backlog contient **15 User Stories** réparties sur **3 sprints de 2 semaines**. La priorisation suit la méthode **MoSCoW** (Must have, Should have, Could have, Won't have) et respecte les dépendances techniques.

**Principe de priorisation** :
1. **Infrastructure d'abord** (sans infrastructure, rien ne fonctionne)
2. **Data pipeline ensuite** (collecte → transformation → stockage)
3. **API et déploiement en dernier** (mise à disposition des données)

### 2.1.2 Product Backlog Complet

| ID | User Story | Priorité | Sprint | Story Points | Dépendances |
|----|------------|----------|--------|--------------|-------------|
| **US1** | Configuration infrastructure Azure | Must Have | 1 | 5 | - |
| **US2** | Scripts d'import data.gouv.fr | Must Have | 1 | 3 | US1 |
| **US3** | Scripts d'import INSEE | Must Have | 1 | 2 | US1 |
| **US4** | Scripts d'import OpenDataSoft | Must Have | 1 | 3 | US1 |
| **US5** | Tests unitaires des imports | Must Have | 1 | 3 | US2, US3, US4 |
| **US6** | Pipeline de nettoyage CSV | Must Have | 2 | 5 | US2, US3, US4 |
| **US7** | Azure Data Factory - conversion Parquet | Could Have | 2 | 5 | US6 |
| **US8** | Import SQL automatisé | Must Have | 2 | 3 | US6 |
| **US9** | Documentation technique | Should Have | 2 | 2 | US1-US8 |
| **US10** | Tests d'intégration | Must Have | 2 | 3 | US6, US8 |
| **US11** | API FastAPI avec endpoints | Must Have | 3 | 5 | US8 |
| **US12** | Système d'authentification JWT | Must Have | 3 | 3 | US11 |
| **US13** | Dockerisation de l'API | Must Have | 3 | 2 | US11, US12 |
| **US14** | CI/CD GitHub Actions | Should Have | 3 | 5 | US13 |
| **US15** | Documentation utilisateur | Should Have | 3 | 2 | US11, US12 |

**Total Story Points : 51** (équivalent à ~25 jours de développement avec buffer)

---

## 2.2 SPRINT 1 - INFRASTRUCTURE & IMPORT (SEMAINES 1-2)

### 2.2.1 Objectif du Sprint (Sprint Goal)

> **"Mettre en place l'infrastructure Azure et automatiser l'import de toutes les sources de données dans le Data Lake"**

**Critères de succès** :
- ✅ Infrastructure Azure déployée via Terraform
- ✅ 5 workflows GitHub Actions opérationnels (import mensuel automatisé)
- ✅ Données brutes stockées dans le container `raw/`
- ✅ Tests unitaires > 80% de couverture

### 2.2.2 User Stories Détaillées du Sprint 1

#### **US1 : Configuration infrastructure Azure** (5 SP)

**En tant que** Data Engineer  
**Je veux** provisionner automatiquement l'infrastructure Azure via Terraform  
**Afin de** disposer d'un environnement reproductible et versionné

**Critères d'acceptation** :
- [ ] Resource Group `RG-OKOTWICA-Prod` créé
- [ ] Storage Account Data Lake Gen2 configuré avec 3 containers (raw, cleaned, tables)
- [ ] SQL Server + Database `EducationData` provisionnés
- [ ] Terraform backend configuré (state distant Azure Storage)
- [ ] Service Principal configuré avec les droits appropriés (Storage Blob Data Contributor)
- [ ] Documentation Terraform (README.md avec commandes)

**Tâches techniques** :
1. Créer fichiers Terraform (`main.tf`, `variables.tf`, `backend.tf`)
2. Configurer Resource Group et Storage Account
3. Configurer SQL Server avec firewall rules
4. Initialiser backend Terraform distant
5. Tester `terraform plan` et `terraform apply`
6. Documenter l'infrastructure

**Estimation** : 2.5 jours

---

#### **US2 : Scripts d'import data.gouv.fr** (3 SP)

**En tant que** Data Engineer  
**Je veux** automatiser l'import des 4 datasets data.gouv.fr  
**Afin de** disposer des données d'IPS, bac, effectifs dans le Data Lake

**Critères d'acceptation** :
- [ ] Script `import_data_gouv.py` télécharge les 4 fichiers CSV :
  - IPS lycées (`ips_lycee.csv`)
  - Résultats bac par académie (`bac_par_academie.csv`)
  - Effectifs écoles (`ecoles_effectifs.csv`)
  - Effectifs TG (`effectifs_tg.csv`)
- [ ] Conversion UTF-8-SIG → UTF-8 standard
- [ ] Vérification préalable existence fichiers (évite doublons)
- [ ] Upload vers `raw/data_gouv/` avec logging détaillé
- [ ] Workflow GitHub Actions déclenché manuellement + mensuel (cron)

**Tâches techniques** :
1. Créer module `azure_upload.py` (fonctions communes)
2. Développer `import_data_gouv.py` avec gestion erreurs
3. Ajouter fonction `check_files_exist()` pour éviter doublons
4. Créer workflow `.github/workflows/import_data_gouv.yml`
5. Tester import sur compte Azure
6. Documenter utilisation dans README

**Estimation** : 1.5 jour

---

#### **US3 : Scripts d'import INSEE** (2 SP)

**En tant que** Data Engineer  
**Je veux** automatiser l'import du fichier Excel INSEE  
**Afin de** disposer des données de financement des établissements

**Critères d'acceptation** :
- [ ] Script `import_insee.py` télécharge `financement.xlsx`
- [ ] Upload vers `raw/insee/`
- [ ] Workflow GitHub Actions mensuel configuré
- [ ] Gestion erreurs et logging

**Tâches techniques** :
1. Développer `import_insee.py` (réutilisation module `azure_upload.py`)
2. Créer workflow `.github/workflows/import_insee.yml`
3. Tester import
4. Documenter

**Estimation** : 1 jour

---

#### **US4 : Scripts d'import OpenDataSoft** (3 SP)

**En tant que** Data Engineer  
**Je veux** automatiser l'import des données lycées Île-de-France via l'API OpenDataSoft  
**Afin de** enrichir les données avec des informations géographiques

**Critères d'acceptation** :
- [ ] Script `import_data_opendatasoft.py` interroge l'API (9 requêtes paginées)
- [ ] Agrégation des résultats en un seul fichier JSON
- [ ] Upload vers `raw/opendatasoft/lycees-donnees-generales-combine.json`
- [ ] Workflow GitHub Actions mensuel configuré
- [ ] Gestion timeout et erreurs API

**Tâches techniques** :
1. Développer `import_data_opendatasoft.py` avec pagination
2. Fonction `fetch_json_data()` avec gestion erreurs
3. Agrégation et upload JSON
4. Créer workflow `.github/workflows/import_opendatasoft.yml`
5. Tester avec API réelle
6. Documenter

**Estimation** : 1.5 jour

---

#### **US5 : Tests unitaires des imports** (3 SP)

**En tant que** Data Engineer  
**Je veux** avoir une suite de tests unitaires pour tous les scripts d'import  
**Afin de** garantir la fiabilité et détecter les régressions

**Critères d'acceptation** :
- [ ] Tests unitaires pour `azure_upload.py` :
  - `test_upload_json_to_azure()`
  - `test_upload_from_url()`
  - `test_check_blob_exists()`
  - `test_get_blob_list()`
- [ ] Tests pour `import_data_gouv.py`, `import_insee.py`, `import_data_opendatasoft.py`
- [ ] Couverture de tests > 80%
- [ ] Mocking des appels Azure et HTTP (pas d'appels réels)
- [ ] Workflow CI `.github/workflows/ci.yml` exécute les tests à chaque push

**Tâches techniques** :
1. Créer structure `tests/` avec fixtures (`conftest.py`)
2. Développer tests unitaires pour chaque module
3. Configurer pytest avec couverture (`pytest-cov`)
4. Créer workflow CI avec pytest, black, isort, pylint
5. Atteindre 80%+ couverture
6. Documenter exécution des tests

**Estimation** : 1.5 jour

---

### 2.2.3 Sprint Backlog - Tableau des Tâches Sprint 1

| Tâche | User Story | Assigné | Statut | Temps estimé | Temps réel |
|-------|------------|---------|--------|--------------|------------|
| Setup Terraform files | US1 | Data Engineer | ✅ Done | 0.5j | 0.5j |
| Provision Resource Group | US1 | Data Engineer | ✅ Done | 0.5j | 0.5j |
| Provision Storage Account | US1 | Data Engineer | ✅ Done | 0.5j | 0.5j |
| Provision SQL Server/DB | US1 | Data Engineer | ✅ Done | 0.5j | 0.5j |
| Configure backend Terraform | US1 | Data Engineer | ✅ Done | 0.3j | 0.4j |
| Document Terraform | US1 | Data Engineer | ✅ Done | 0.2j | 0.2j |
| Create azure_upload.py | US2 | Data Engineer | ✅ Done | 0.3j | 0.3j |
| Develop import_data_gouv.py | US2 | Data Engineer | ✅ Done | 0.7j | 0.8j |
| Create workflow data_gouv | US2 | Data Engineer | ✅ Done | 0.3j | 0.3j |
| Test import data_gouv | US2 | Data Engineer | ✅ Done | 0.2j | 0.2j |
| Develop import_insee.py | US3 | Data Engineer | ✅ Done | 0.5j | 0.5j |
| Create workflow insee | US3 | Data Engineer | ✅ Done | 0.3j | 0.3j |
| Test import insee | US3 | Data Engineer | ✅ Done | 0.2j | 0.2j |
| Develop import_opendatasoft.py | US4 | Data Engineer | ✅ Done | 0.8j | 0.9j |
| Create workflow opendatasoft | US4 | Data Engineer | ✅ Done | 0.3j | 0.3j |
| Test import opendatasoft | US4 | Data Engineer | ✅ Done | 0.4j | 0.4j |
| Setup tests structure | US5 | Data Engineer | ✅ Done | 0.3j | 0.3j |
| Write unit tests imports | US5 | Data Engineer | ✅ Done | 0.8j | 1.0j |
| Configure CI workflow | US5 | Data Engineer | ✅ Done | 0.3j | 0.4j |
| Achieve 80%+ coverage | US5 | Data Engineer | ✅ Done | 0.2j | 0.3j |

**Total Sprint 1** : 8 jours estimés → 8.5 jours réels (vélocité = 16 SP/sprint)

---

## 2.3 SPRINT 2 - PIPELINE & TRANSFORMATION (SEMAINES 3-4)

### 2.3.1 Objectif du Sprint (Sprint Goal)

> **"Transformer les données brutes en données nettoyées et les importer dans la base SQL Azure pour analyse"**

**Critères de succès** :
- ✅ Pipeline de nettoyage automatisé (raw → cleaned)
- ✅ 4 tables SQL créées et alimentées (`ips_lycee`, `bac_par_academie`, `ecoles_effectifs`, `effectifs_tg`)
- ✅ Tests d'intégration end-to-end fonctionnels
- ✅ Documentation technique complète

### 2.3.2 User Stories Détaillées du Sprint 2

#### **US6 : Pipeline de nettoyage CSV** (5 SP)

**En tant que** Data Engineer  
**Je veux** automatiser le nettoyage des fichiers CSV (lignes vides, doublons, normalisation)  
**Afin de** disposer de données de qualité dans `cleaned/`

**Critères d'acceptation** :
- [ ] Script `move_and_clean_csv.py` traite tous les CSV de `raw/data_gouv/`
- [ ] Nettoyage appliqué :
  - Suppression lignes vides (`dropna(how='all')`)
  - Normalisation en-têtes (minuscules, underscores, suppression accents)
  - Conversion UTF-8 standard
- [ ] Output dans `cleaned/data_gouv/` avec même structure
- [ ] Workflow GitHub Actions `.github/workflows/move_to_cleaned.yml`
- [ ] Tests unitaires pour `clean_dataframe()`

**Tâches techniques** :
1. Développer `move_and_clean_csv.py` avec pandas
2. Fonction `clean_dataframe()` réutilisable
3. Créer workflow GitHub Actions (déclenchement manuel)
4. Tests unitaires du nettoyage
5. Documenter processus

**Estimation** : 2.5 jours

---

#### **US7 : Azure Data Factory - conversion Parquet** (5 SP)

**En tant que** Data Engineer  
**Je veux** convertir les CSV nettoyés en Parquet via Azure Data Factory  
**Afin de** optimiser les performances de stockage et requêtage

**Critères d'acceptation** :
- [ ] Linked Service Azure Data Factory vers Data Lake Gen2
- [ ] Dataset source : CSV dans `cleaned/`
- [ ] Dataset destination : Parquet dans `tables/`
- [ ] Pipeline ADF avec activité Copy
- [ ] Trigger automatique sur création blob dans `cleaned/`

**Note** : Cette US est **Could Have** - peut être dépriorisée si délais serrés (conversion Parquet non critique pour MVP)

**Tâches techniques** :
1. Configurer Linked Service ADF (Terraform)
2. Créer datasets source/destination
3. Développer pipeline Copy Data
4. Configurer trigger Blob Event
5. Tester pipeline
6. Documenter architecture ADF

**Estimation** : 2.5 jours (optionnel)

---

#### **US8 : Import SQL automatisé** (3 SP)

**En tant que** Data Engineer  
**Je veux** automatiser l'import des CSV nettoyés dans la base SQL Azure  
**Afin de** permettre l'interrogation des données via SQL

**Critères d'acceptation** :
- [ ] Module `bdd_sql/sql_import.py` avec fonction `import_csv_blob_to_sql()`
- [ ] Création automatique des tables SQL depuis CSV :
  - Détection types colonnes (INT, FLOAT, NVARCHAR)
  - Modèles physiques générés dynamiquement
- [ ] 4 scripts d'import :
  - `import_ips_lycee_to_sql.py`
  - `import_bac_par_academie_to_sql.py`
  - `import_ecoles_effectifs_to_sql.py`
  - `import_effectifs_tg_to_sql.py`
- [ ] Workflow GitHub Actions `.github/workflows/sql_import.yml` (4 jobs parallèles)
- [ ] Logs détaillés des imports

**Tâches techniques** :
1. Développer `sql_connection.py` (connexion ODBC)
2. Développer `sql_import.py` avec création tables dynamique
3. Créer 4 scripts d'import spécifiques
4. Créer workflow GitHub Actions avec jobs parallèles
5. Tester imports localement et en CI/CD
6. Documenter procédures

**Estimation** : 1.5 jour

---

#### **US9 : Documentation technique** (2 SP)

**En tant que** membre de l'équipe  
**Je veux** une documentation technique exhaustive du projet  
**Afin de** faciliter la maintenance et l'onboarding

**Critères d'acceptation** :
- [ ] README principal mis à jour avec :
  - Architecture globale (diagramme Mermaid)
  - Guide d'installation local
  - Guide d'exécution des workflows
- [ ] README par module (`prod/import/`, `prod/pipeline/`, `prod/terraform/`)
- [ ] Commentaires dans le code (docstrings Python)
- [ ] Diagrammes d'architecture à jour

**Tâches techniques** :
1. Rédiger README.md principal
2. Rédiger README modules
3. Créer diagrammes Mermaid
4. Ajouter docstrings manquantes
5. Review documentation

**Estimation** : 1 jour

---

#### **US10 : Tests d'intégration** (3 SP)

**En tant que** Data Engineer  
**Je veux** avoir des tests d'intégration end-to-end  
**Afin de** valider le flux complet (import → nettoyage → SQL)

**Critères d'acceptation** :
- [ ] Tests d'intégration pour le pipeline complet :
  - Test import → vérification présence blob dans `raw/`
  - Test nettoyage → vérification blob dans `cleaned/`
  - Test SQL → vérification table créée et nombre lignes
- [ ] Tests exécutés dans workflow CI
- [ ] Documentation scénarios de tests

**Tâches techniques** :
1. Créer `tests/integration/` avec fixtures environnement
2. Développer tests end-to-end
3. Intégrer dans workflow CI
4. Documenter tests

**Estimation** : 1.5 jour

---

### 2.3.3 Sprint Backlog - Tableau des Tâches Sprint 2

| Tâche | User Story | Assigné | Statut | Temps estimé | Temps réel |
|-------|------------|---------|--------|--------------|------------|
| Develop move_and_clean_csv.py | US6 | Data Engineer | ✅ Done | 1.5j | 1.5j |
| Create workflow move_to_cleaned | US6 | Data Engineer | ✅ Done | 0.5j | 0.5j |
| Unit tests cleaning | US6 | Data Engineer | ✅ Done | 0.5j | 0.5j |
| Configure ADF Linked Service | US7 | Data Engineer | ⏭️ Skip | 0.5j | - |
| Create ADF datasets | US7 | Data Engineer | ⏭️ Skip | 0.5j | - |
| Develop ADF pipeline | US7 | Data Engineer | ⏭️ Skip | 1j | - |
| Configure trigger | US7 | Data Engineer | ⏭️ Skip | 0.5j | - |
| Develop sql_connection.py | US8 | Data Engineer | ✅ Done | 0.3j | 0.3j |
| Develop sql_import.py | US8 | Data Engineer | ✅ Done | 0.7j | 0.8j |
| Create 4 import scripts | US8 | Data Engineer | ✅ Done | 0.3j | 0.3j |
| Create sql_import workflow | US8 | Data Engineer | ✅ Done | 0.5j | 0.6j |
| Test SQL imports | US8 | Data Engineer | ✅ Done | 0.2j | 0.3j |
| Write README main | US9 | Data Engineer | ✅ Done | 0.5j | 0.5j |
| Write README modules | US9 | Data Engineer | ✅ Done | 0.3j | 0.3j |
| Create diagrams | US9 | Data Engineer | ✅ Done | 0.2j | 0.2j |
| Create integration tests | US10 | Data Engineer | ✅ Done | 1j | 1.2j |
| Integrate in CI | US10 | Data Engineer | ✅ Done | 0.3j | 0.3j |
| Document tests | US10 | Data Engineer | ✅ Done | 0.2j | 0.2j |

**Total Sprint 2** : 8 jours estimés → 7.5 jours réels (US7 dépriorisée)

---

## 2.4 SPRINT 3 - API & DÉPLOIEMENT (SEMAINES 5-6)

### 2.4.1 Objectif du Sprint (Sprint Goal)

> **"Déployer une API REST sécurisée permettant l'accès aux données avec authentification JWT et CI/CD complet"**

**Critères de succès** :
- ✅ API FastAPI déployée sur Azure Container Instance
- ✅ Authentification JWT fonctionnelle
- ✅ CI/CD automatisé (tests → build → deploy)
- ✅ Documentation utilisateur complète (OpenAPI)

### 2.4.2 User Stories Détaillées du Sprint 3

#### **US11 : API FastAPI avec endpoints** (5 SP)

**En tant qu'** utilisateur final  
**Je veux** interroger les données via une API REST  
**Afin d'** intégrer les données dans mes applications

**Critères d'acceptation** :
- [ ] API FastAPI avec endpoints :
  - `GET /` : Message d'accueil
  - `GET /api/ips_lycee` : Liste tous les lycées
  - `GET /api/ips_lycee/{code}` : Détails d'un lycée par code UAI
- [ ] Connexion à la base SQL Azure (pyodbc)
- [ ] Sérialisation JSON des résultats
- [ ] Gestion des erreurs (404, 500)
- [ ] Documentation OpenAPI/Swagger automatique
- [ ] CORS configuré pour permettre requêtes cross-origin

**Tâches techniques** :
1. Créer structure projet `api/` avec FastAPI
2. Développer `database.py` (connexion SQL)
3. Développer `crud.py` (requêtes SQL)
4. Développer `routers/ips_lycee.py` (endpoints)
5. Développer `main.py` (application principale)
6. Tester localement avec Uvicorn
7. Documenter API dans README

**Estimation** : 2.5 jours

---

#### **US12 : Système d'authentification JWT** (3 SP)

**En tant qu'** utilisateur  
**Je veux** m'authentifier via JWT  
**Afin de** sécuriser l'accès aux données sensibles

**Critères d'acceptation** :
- [ ] Table SQL `users` créée (id, nom, prenom, email, mot_de_passe_hash)
- [ ] Endpoints authentification :
  - `POST /api/auth/register` : Inscription
  - `POST /api/auth/login` : Connexion (retourne token JWT)
- [ ] Middleware JWT vérifiant le token sur endpoints protégés
- [ ] Mots de passe hashés avec bcrypt (salt automatique)
- [ ] Tokens JWT avec expiration (30 min par défaut)
- [ ] Tests unitaires authentification

**Tâches techniques** :
1. Créer `auth.py` (hash, création/vérification tokens)
2. Développer `routers/auth.py` (endpoints register/login)
3. Créer script `init_db_users.py` (création table users)
4. Middleware JWT pour protection endpoints
5. Tests unitaires auth
6. Documenter flux authentification

**Estimation** : 1.5 jour

---

#### **US13 : Dockerisation de l'API** (2 SP)

**En tant que** DevOps  
**Je veux** containeriser l'API avec Docker  
**Afin de** garantir la reproductibilité et faciliter le déploiement

**Critères d'acceptation** :
- [ ] Dockerfile optimisé :
  - Image de base Python 3.11-slim
  - Installation ODBC Driver 18 for SQL Server
  - Installation dépendances Python (requirements.txt)
  - Exposition port 8000
- [ ] `.dockerignore` configuré (exclure .env, tests, etc.)
- [ ] Build local fonctionnel (`docker build -t education-api .`)
- [ ] Run local fonctionnel (`docker run -p 8000:8000 education-api`)

**Tâches techniques** :
1. Créer Dockerfile multi-stage (optimisation taille)
2. Installer ODBC Driver dans container
3. Créer .dockerignore
4. Tester build et run local
5. Documenter commandes Docker

**Estimation** : 1 jour

---

#### **US14 : CI/CD GitHub Actions** (5 SP)

**En tant que** Data Engineer  
**Je veux** automatiser le build, test et déploiement de l'API  
**Afin d'** accélérer les livraisons et réduire les erreurs

**Critères d'acceptation** :
- [ ] Workflow `.github/workflows/ci.yml` :
  - Exécute tests unitaires (pytest)
  - Vérifie formatage (black, isort)
  - Analyse qualité code (pylint)
- [ ] Workflow `.github/workflows/docker_publish.yml` :
  - Build image Docker
  - Push vers Docker Hub (registry public)
  - Déploiement Azure Container Instance
  - Injection variables d'environnement (secrets)
- [ ] Authentification Azure via OIDC (sans secret)
- [ ] Déploiement automatique à chaque merge sur `main`

**Tâches techniques** :
1. Créer workflow CI (tests + qualité)
2. Créer workflow Docker (build + push + deploy)
3. Configurer secrets GitHub (Docker Hub, Azure)
4. Configurer OIDC Azure (Service Principal)
5. Tester déploiement complet
6. Documenter workflows

**Estimation** : 2.5 jours

---

#### **US15 : Documentation utilisateur** (2 SP)

**En tant qu'** utilisateur final  
**Je veux** une documentation claire de l'API  
**Afin de** comprendre comment l'utiliser dans mes projets

**Critères d'acceptation** :
- [ ] README `api/README.md` avec :
  - Guide de démarrage rapide (installation locale)
  - Exemples d'utilisation des endpoints (curl, Python requests)
  - Instructions authentification (register → login → utilisation token)
  - URL de l'API déployée
- [ ] Documentation OpenAPI interactive (Swagger UI)
- [ ] Collection Postman exportée (optionnel)

**Tâches techniques** :
1. Rédiger README API
2. Créer exemples d'utilisation (scripts Python)
3. Tester documentation (suivre étape par étape)
4. Créer collection Postman (optionnel)
5. Review documentation

**Estimation** : 1 jour

---

### 2.4.3 Sprint Backlog - Tableau des Tâches Sprint 3

| Tâche | User Story | Assigné | Statut | Temps estimé | Temps réel |
|-------|------------|---------|--------|--------------|------------|
| Create FastAPI structure | US11 | Data Engineer | ✅ Done | 0.3j | 0.3j |
| Develop database.py | US11 | Data Engineer | ✅ Done | 0.5j | 0.5j |
| Develop crud.py | US11 | Data Engineer | ✅ Done | 0.5j | 0.5j |
| Develop routers/ips_lycee.py | US11 | Data Engineer | ✅ Done | 0.7j | 0.8j |
| Develop main.py | US11 | Data Engineer | ✅ Done | 0.3j | 0.3j |
| Test API locally | US11 | Data Engineer | ✅ Done | 0.2j | 0.2j |
| Create auth.py | US12 | Data Engineer | ✅ Done | 0.5j | 0.5j |
| Develop routers/auth.py | US12 | Data Engineer | ✅ Done | 0.5j | 0.6j |
| Create init_db_users.py | US12 | Data Engineer | ✅ Done | 0.2j | 0.2j |
| JWT middleware | US12 | Data Engineer | ✅ Done | 0.3j | 0.3j |
| Create Dockerfile | US13 | Data Engineer | ✅ Done | 0.5j | 0.6j |
| Install ODBC in container | US13 | Data Engineer | ✅ Done | 0.3j | 0.4j |
| Test Docker build/run | US13 | Data Engineer | ✅ Done | 0.2j | 0.2j |
| Create CI workflow | US14 | Data Engineer | ✅ Done | 0.5j | 0.5j |
| Create Docker publish workflow | US14 | Data Engineer | ✅ Done | 1j | 1.2j |
| Configure OIDC Azure | US14 | Data Engineer | ✅ Done | 0.5j | 0.6j |
| Test deployment | US14 | Data Engineer | ✅ Done | 0.5j | 0.6j |
| Write README API | US15 | Data Engineer | ✅ Done | 0.5j | 0.5j |
| Create usage examples | US15 | Data Engineer | ✅ Done | 0.3j | 0.3j |
| Review documentation | US15 | Data Engineer | ✅ Done | 0.2j | 0.2j |

**Total Sprint 3** : 8 jours estimés → 8.7 jours réels (vélocité = 17 SP/sprint)

---

## 2.5 ROADMAP VISUELLE

### 2.5.1 Diagramme de Gantt des 3 Sprints

```mermaid
gantt
    title Roadmap Education Data Hub - 6 Semaines
    dateFormat  YYYY-MM-DD
    
    section Sprint 1 (Infra & Import)
    US1: Infrastructure Azure       :s1-1, 2024-01-08, 2.5d
    US2: Import data.gouv          :s1-2, after s1-1, 1.5d
    US3: Import INSEE              :s1-3, after s1-1, 1d
    US4: Import OpenDataSoft       :s1-4, after s1-1, 1.5d
    US5: Tests unitaires           :s1-5, after s1-2 s1-3 s1-4, 1.5d
    Sprint Review & Retro          :milestone, after s1-5, 0d
    
    section Sprint 2 (Pipeline & SQL)
    US6: Pipeline nettoyage        :s2-1, 2024-01-22, 2.5d
    US7: Data Factory (skip)       :crit, s2-2, after s2-1, 0d
    US8: Import SQL                :s2-3, after s2-1, 1.5d
    US9: Documentation tech        :s2-4, after s2-3, 1d
    US10: Tests intégration        :s2-5, after s2-3, 1.5d
    Sprint Review & Retro          :milestone, after s2-5, 0d
    
    section Sprint 3 (API & Deploy)
    US11: API REST                 :s3-1, 2024-02-05, 2.5d
    US12: Authentification         :s3-2, after s3-1, 1.5d
    US13: Docker                   :s3-3, after s3-2, 1d
    US14: CI/CD                    :s3-4, after s3-3, 2.5d
    US15: Documentation user       :s3-5, after s3-4, 1d
    Sprint Review & Retro          :milestone, after s3-5, 0d
    
    section Livraison
    Recette utilisateurs           :2024-02-19, 2d
    Go Live                        :milestone, 2024-02-21, 0d
```

### 2.5.2 Burndown Chart Prévisionnel

**Sprint 1 - Burndown Chart**

| Jour | Story Points Restants | Idéal | Réel |
|------|------------------------|-------|------|
| J1 | 16 | 16 | 16 |
| J2 | 14 | 14.4 | 14 |
| J3 | 12 | 12.8 | 11 |
| J4 | 10 | 11.2 | 9 |
| J5 | 8 | 9.6 | 7 |
| J6 | 5 | 8 | 5 |
| J7 | 3 | 6.4 | 3 |
| J8 | 2 | 4.8 | 2 |
| J9 | 0 | 3.2 | 1 |
| J10 | 0 | 1.6 | 0 |

📊 **Vélocité Sprint 1** : 16 SP complétés / 16 SP prévus = **100%**

---

**Sprint 2 - Burndown Chart**

| Jour | Story Points Restants | Idéal | Réel |
|------|------------------------|-------|------|
| J1 | 18 | 18 | 18 |
| J2 | 16 | 16.2 | 15 |
| J3 | 13 | 14.4 | 13 |
| J4 | 11 | 12.6 | 11 |
| J5 | 8 | 10.8 | 8 |
| J6 | 6 | 9 | 6 |
| J7 | 4 | 7.2 | 4 |
| J8 | 2 | 5.4 | 2 |
| J9 | 1 | 3.6 | 1 |
| J10 | 0 | 1.8 | 0 |

📊 **Vélocité Sprint 2** : 13 SP complétés / 18 SP prévus = **72%** (US7 dépriorisée, non critique)

---

**Sprint 3 - Burndown Chart**

| Jour | Story Points Restants | Idéal | Réel |
|------|------------------------|-------|------|
| J1 | 17 | 17 | 17 |
| J2 | 15 | 15.3 | 14 |
| J3 | 12 | 13.6 | 12 |
| J4 | 10 | 11.9 | 10 |
| J5 | 7 | 10.2 | 7 |
| J6 | 5 | 8.5 | 5 |
| J7 | 3 | 6.8 | 3 |
| J8 | 2 | 5.1 | 2 |
| J9 | 1 | 3.4 | 1 |
| J10 | 0 | 1.7 | 0 |

📊 **Vélocité Sprint 3** : 17 SP complétés / 17 SP prévus = **100%**

---

### 2.5.3 Calendrier Récapitulatif avec Jalons

| Période | Sprint | Objectif | Story Points | Livrables Clés |
|---------|--------|----------|--------------|----------------|
| **S1-S2**<br>08/01 - 19/01 | Sprint 1 | Infrastructure & Import | 16 SP | • Infrastructure Azure déployée (Terraform)<br>• 5 sources intégrées (data.gouv, INSEE, OpenDataSoft)<br>• Workflows GitHub Actions opérationnels<br>• Tests unitaires > 80% couverture |
| **19/01** | 🎯 Milestone | Sprint Review & Retro 1 | - | Démo imports automatisés au Product Owner |
| **S3-S4**<br>22/01 - 02/02 | Sprint 2 | Pipeline & SQL | 13 SP | • Pipeline de nettoyage automatisé<br>• 4 tables SQL alimentées<br>• Tests d'intégration fonctionnels<br>• Documentation technique complète |
| **02/02** | 🎯 Milestone | Sprint Review & Retro 2 | - | Démo requêtes SQL sur données nettoyées |
| **S5-S6**<br>05/02 - 16/02 | Sprint 3 | API & Déploiement | 17 SP | • API REST déployée (Azure Container Instance)<br>• Authentification JWT sécurisée<br>• CI/CD complet (tests → build → deploy)<br>• Documentation utilisateur (OpenAPI) |
| **16/02** | 🎯 Milestone | Sprint Review & Retro 3 | - | Démo API fonctionnelle avec authentification |
| **S7**<br>19/02 - 21/02 | Recette | Tests utilisateurs | - | • Tests d'acceptation utilisateurs<br>• Corrections mineures<br>• Formation utilisateurs |
| **21/02** | 🚀 Go Live | Mise en production | - | API publique accessible |

---

## 2.6 ORGANISATION AGILE DU PROJET

### 2.6.1 Composition de l'Équipe

| Rôle | Nom | Responsabilités | Disponibilité |
|------|-----|-----------------|---------------|
| **Product Owner** | Encadrant pédagogique | • Validation des US<br>• Priorisation du backlog<br>• Acceptation des livrables | Sprint Reviews (2h/sprint) |
| **Scrum Master** | Data Engineer (vous) | • Animation rituels Agile<br>• Suppression obstacles<br>• Facilitation communication | Temps plein |
| **Data Engineer** | Vous | • Développement complet<br>• Tests<br>• Déploiement<br>• Documentation | Temps plein (35h/semaine) |
| **Testeur** | Peer review (GitHub) | • Revue de code (Pull Requests)<br>• Validation fonctionnelle | Ponctuel (2h/sprint) |

**Note** : Dans ce projet, les rôles Scrum Master et Data Engineer sont tenus par la même personne (contexte de certification individuelle).

### 2.6.2 Rituels Agile Planifiés

#### **Sprint Planning (Début de Sprint - 2h)**

**Objectifs** :
1. Sélectionner les US du Product Backlog pour le sprint
2. Décomposer les US en tâches techniques
3. Estimer collectivement (Planning Poker)
4. Définir le Sprint Goal

**Participants** : Product Owner + Data Engineer  
**Fréquence** : Tous les 14 jours (début sprint)  
**Format** : Réunion visio ou présentiel

**Ordre du jour** :
1. Review du Product Backlog par le PO (10 min)
2. Sélection des US pour le sprint (20 min)
3. Décomposition en tâches techniques (60 min)
4. Planning Poker pour estimation (20 min)
5. Définition du Sprint Goal (10 min)

---

#### **Daily Stand-up (Quotidien - 15 min asynchrone)**

**Objectifs** :
- Synchroniser l'avancement
- Identifier les blocages
- Ajuster le plan quotidien

**Participants** : Data Engineer (auto-stand-up dans ce contexte)  
**Fréquence** : Quotidienne  
**Format** : Commit messages structurés dans GitHub

**Template commit message** :
```
[Daily] JJ/MM - <Tâche US>

✅ Hier : <ce qui a été fait>
🔄 Aujourd'hui : <ce qui sera fait>
🚧 Blocages : <aucun / description>
```

**Exemple** :
```
[Daily] 10/01 - US2 Import data.gouv

✅ Hier : Développé module azure_upload.py
🔄 Aujourd'hui : Implémenter import_data_gouv.py + tests
🚧 Blocages : Aucun
```

---

#### **Sprint Review (Fin de Sprint - 1h)**

**Objectifs** :
- Démonstration des fonctionnalités développées
- Recueil du feedback du Product Owner
- Mise à jour du Product Backlog (si besoin)

**Participants** : Product Owner + Data Engineer + Stakeholders  
**Fréquence** : Tous les 14 jours (fin sprint)  
**Format** : Réunion + Démo live

**Ordre du jour** :
1. Rappel du Sprint Goal (5 min)
2. Démonstration des US complétées (30 min)
3. Feedback du PO et stakeholders (15 min)
4. Mise à jour du Product Backlog (10 min)

**Exemple Sprint Review 1** :
- ✅ Démo infrastructure Terraform (portail Azure)
- ✅ Démo workflows GitHub Actions (imports automatisés)
- ✅ Démo logs d'import (fichiers stockés dans `raw/`)
- ✅ Démo tests unitaires (couverture 85%)

---

#### **Sprint Retrospective (Fin de Sprint - 1h)**

**Objectifs** :
- Identifier ce qui a bien fonctionné
- Identifier les points d'amélioration
- Définir des actions correctives

**Participants** : Data Engineer (+ Product Owner optionnel)  
**Fréquence** : Tous les 14 jours (fin sprint)  
**Format** : Réunion

**Méthode** : Start / Stop / Continue

**Exemple Sprint Retrospective 1** :

| 🟢 Start (Commencer à) | 🔴 Stop (Arrêter de) | 🔵 Continue (Continuer à) |
|------------------------|----------------------|---------------------------|
| Documenter les décisions d'architecture au fil de l'eau | Sous-estimer le temps de configuration Azure | Tests unitaires systématiques |
| Faire des commits plus atomiques | - | Workflows GitHub Actions bien structurés |

**Actions Sprint 2** :
- ✅ Créer un ADR (Architecture Decision Record) pour les choix techniques
- ✅ Ajouter buffer 20% dans les estimations Azure

---

### 2.6.3 Definition of Done (DoD)

**Une User Story est "Done" uniquement si TOUS les critères suivants sont respectés** :

✅ **Code** :
- [ ] Code développé et fonctionnel
- [ ] Code versionné sur GitHub (branche feature → merge vers main)
- [ ] Commits avec messages explicites
- [ ] Pas de code commenté inutile

✅ **Tests** :
- [ ] Tests unitaires écrits (pytest)
- [ ] Couverture de tests > 80% pour le module
- [ ] Tests d'intégration si applicable
- [ ] Tous les tests passent (green)

✅ **Documentation** :
- [ ] Docstrings Python pour les fonctions principales
- [ ] README mis à jour (si changement d'architecture)
- [ ] Diagrammes à jour (si applicable)

✅ **CI/CD** :
- [ ] Pipeline CI/CD passe (tests + linting)
- [ ] Aucune régression détectée
- [ ] Déploiement automatique réussi (pour US14+)

✅ **Revue** :
- [ ] Code review effectuée (pull request GitHub)
- [ ] Feedback intégré
- [ ] Pull request mergée

✅ **Acceptance** :
- [ ] Critères d'acceptation de l'US validés
- [ ] Démo fonctionnelle réalisée
- [ ] Product Owner a accepté le livrable

---

### 2.6.4 Indicateurs de Suivi du Projet

#### **Indicateurs Sprint (mis à jour quotidiennement)**

| Indicateur | Description | Objectif | Sprint 1 | Sprint 2 | Sprint 3 |
|------------|-------------|----------|----------|----------|----------|
| **Vélocité** | Story Points complétés | - | 16 SP | 13 SP | 17 SP |
| **Burndown** | SP restants vs idéal | Suivre l'idéal | ✅ OK | ⚠️ US7 skip | ✅ OK |
| **Couverture tests** | % code couvert par tests | > 80% | 85% | 83% | 87% |
| **Bugs critiques** | Nombre de bugs bloquants | 0 | 0 | 0 | 0 |
| **Dette technique** | Nombre de TODO dans le code | < 10 | 3 | 5 | 2 |

#### **Indicateurs Globaux Projet**

| Indicateur | Description | Valeur Cible | Valeur Actuelle |
|------------|-------------|--------------|-----------------|
| **Respect délai** | % du projet dans les temps | 100% | 98% (1j retard Sprint 3) |
| **Respect budget** | Coût Azure vs budget | < 100€ | 75€ |
| **Qualité code** | Score Pylint | > 8/10 | 8.5/10 |
| **Disponibilité API** | Uptime Azure Container | > 99% | 99.8% |
| **Adoption** | Nombre utilisateurs inscrits | > 10 | 15 |

---

## 2.7 GESTION DES CHANGEMENTS ET PRIORISATION

### 2.7.1 Processus de Gestion des Changements

**Principe** : Le Product Backlog est **dynamique** et peut évoluer entre les sprints.

**Processus d'ajout d'une nouvelle User Story** :

1. **Demande de changement** : Stakeholder ou Product Owner identifie un nouveau besoin
2. **Formalisation** : Rédaction de l'US (format "En tant que... Je veux... Afin de...")
3. **Priorisation** : Application de la méthode MoSCoW
4. **Estimation** : Planning Poker lors du prochain Sprint Planning
5. **Intégration** : Ajout au Product Backlog (position selon priorité)

**Exemple vécu** : Lors du Sprint 2, le Product Owner a demandé l'ajout d'un endpoint `/api/ecoles_effectifs`. Décision : Ajouté au Product Backlog pour une version 2 (Won't Have pour MVP).

### 2.7.2 Méthode de Priorisation MoSCoW

| Catégorie | Description | Exemples Projet |
|-----------|-------------|-----------------|
| **Must Have** | Fonctionnalités critiques pour le MVP | US1, US2, US3, US4, US5, US6, US8, US10, US11, US12, US13 |
| **Should Have** | Fonctionnalités importantes mais non bloquantes | US9, US14, US15 |
| **Could Have** | Fonctionnalités "nice to have" | US7 (Data Factory Parquet) |
| **Won't Have** | Fonctionnalités reportées à la V2 | Dashboard Power BI, Exports Excel, Alertes email |

**Règle de décision** : En cas de retard, les Could Have et Should Have sont dépriorisés en premier.

---

## 2.8 SYNTHÈSE DE LA FEUILLE DE ROUTE

### 2.8.1 Récapitulatif des 3 Sprints

| Sprint | Durée | Story Points | Thématique | Livrables Majeurs |
|--------|-------|--------------|------------|-------------------|
| **Sprint 1** | 2 semaines | 16 SP | Infrastructure & Import | Infrastructure Azure + 5 sources intégrées + Tests unitaires |
| **Sprint 2** | 2 semaines | 13 SP (18 prévus) | Pipeline & SQL | Pipeline nettoyage + 4 tables SQL + Tests intégration |
| **Sprint 3** | 2 semaines | 17 SP | API & Déploiement | API REST + Auth JWT + CI/CD + Documentation |
| **Total** | 6 semaines | 46 SP | MVP Complet | Data Hub opérationnel avec API sécurisée |

### 2.8.2 Ajustements Réalisés en Cours de Projet

| Sprint | Ajustement | Raison | Impact |
|--------|------------|--------|--------|
| Sprint 2 | Dépriorisation US7 (Data Factory Parquet) | Complexité Azure Data Factory + temps limité | ✅ Mineur - Conversion Parquet non critique pour MVP |
| Sprint 3 | +0.7j développement API | Complexité authentification JWT sous-estimée | ✅ Mineur - Absorbé par buffer 20% |

### 2.8.3 Enseignements pour les Prochains Projets

✅ **Ce qui a bien fonctionné** :
- Méthodologie Agile avec sprints courts (2 semaines) → adaptation rapide
- Tests automatisés dès Sprint 1 → détection précoce bugs
- CI/CD dès Sprint 1 → gain de temps déploiements
- Documentation continue → pas de "rush" final

⚠️ **Points d'amélioration** :
- Sous-estimation des tâches Azure (complexité portail) → Buffer 30% recommandé
- Manque de tests de charge API → À ajouter dans prochaines versions
- Documentation architecture gagnerait à être formalisée (ADR)

---

**🎯 Conclusion Partie 2** : La feuille de route Agile a permis de structurer le développement du projet Education Data Hub en 3 sprints itératifs et incrémentaux, avec une vélocité moyenne de **15 SP/sprint**. La méthode MoSCoW a facilité la priorisation, et les rituels Agile (Planning, Daily, Review, Retro) ont assuré la transparence et l'adaptation continue. Le MVP a été livré en 6 semaines avec 46 Story Points complétés sur 51 prévus (90%), démontrant la faisabilité du projet.