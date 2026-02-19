# PARTIE 1 : CONTEXTE ET AVANT-PROJET
## Projet Education Data Hub - Lancement d'un Projet Data

---

## 1.1 PRÉSENTATION DU PROJET EDUCATION DATA HUB

### 1.1.1 Contexte Stratégique

Le secteur éducatif français génère d'importantes quantités de données issues de multiples sources institutionnelles (Ministère de l'Éducation Nationale, INSEE, académies, établissements). Ces données sont actuellement **dispersées, hétérogènes et difficiles d'accès**, ce qui limite leur exploitation pour :

- **L'analyse comparative** des performances des établissements
- **La prise de décision** des acteurs éducatifs (chefs d'établissement, collectivités)
- **La recherche académique** sur les inégalités scolaires
- **Le pilotage stratégique** des politiques éducatives

**Opportunité identifiée** : Créer un hub centralisé de données éducatives permettant l'accès, l'analyse et la visualisation de ces informations de manière sécurisée et conforme au RGPD.

### 1.1.2 Expression du Besoin Initial

**Commanditaire** : Direction des systèmes d'information (fictive pour l'exercice de certification)

**Besoin exprimé** :
> "Nous souhaitons centraliser les données éducatives publiques (IPS des lycées, effectifs des écoles, résultats du baccalauréat, financements) dans une infrastructure moderne permettant leur exploitation par des outils d'analyse (Power BI, Python, R). Le système doit être automatisé, évolutif et respecter les normes de sécurité et de confidentialité."

**Contraintes identifiées** :
- Budget limité : Infrastructure cloud uniquement
- Délai : 6 semaines pour un MVP (Minimum Viable Product)
- Conformité : RGPD obligatoire (données établissements publiques)
- Accessibilité : Documentation en français, interfaces claires

---

## 1.2 OBJECTIFS DU PROJET (MÉTHODE SMART)

| Critère | Description |
|---------|-------------|
| **Spécifique** | Créer un Data Hub centralisé pour les données éducatives françaises avec collecte automatisée, stockage structuré (Data Lake Azure), transformation ETL et mise à disposition via API REST |
| **Mesurable** | • Intégrer **5 sources de données** (data.gouv.fr, INSEE, OpenDataSoft)<br>• Automatiser **100% des imports** via workflows GitHub Actions<br>• Atteindre **85% de couverture de tests**<br>• Mettre en ligne une **API REST documentée** (OpenAPI)<br>• Réduire le temps d'accès aux données de **plusieurs heures à quelques secondes** |
| **Acceptable** | • Validation par le Product Owner à chaque sprint<br>• Réponse aux besoins des analystes de données éducatives<br>• Amélioration de la prise de décision des établissements |
| **Réaliste** | • Infrastructure Azure existante (compte Azure actif)<br>• Compétences techniques disponibles (Python, SQL, Azure, Terraform)<br>• Sources de données accessibles publiquement<br>• Budget maîtrisé (services Azure gratuits/faible coût) |
| **Temporellement défini** | • **Sprint 1** (S1-S2) : Infrastructure & Import<br>• **Sprint 2** (S3-S4) : Pipeline ETL & Transformation<br>• **Sprint 3** (S5-S6) : API & Déploiement<br>• **Date de livraison** : 6 semaines après le lancement |

---

## 1.3 PÉRIMÈTRE FONCTIONNEL DU PROJET

### 1.3.1 Fonctionnalités Incluses dans le MVP

#### **Bloc 1 : Collecte Automatisée des Données**
- Import mensuel automatisé depuis **data.gouv.fr** :
  - IPS (Indice de Position Sociale) des lycées
  - Résultats du baccalauréat par académie
  - Effectifs des écoles primaires
  - Effectifs des terminales générales
- Import mensuel automatisé depuis **INSEE** :
  - Financement des établissements scolaires
- Import mensuel automatisé depuis **OpenDataSoft** :
  - Données générales des lycées d'Île-de-France

**Critères d'acceptation** :
- ✅ Workflows GitHub Actions déclenchés le 1er de chaque mois
- ✅ Vérification préalable de l'existence des fichiers (éviter doublons)
- ✅ Logs détaillés des opérations d'import
- ✅ Gestion des erreurs avec alertes

#### **Bloc 2 : Stockage dans un Data Lake Azure**
- Architecture **Medallion** (3 couches) :
  - **raw/** : Données brutes (formats originaux : CSV, JSON, XLSX)
  - **cleaned/** : Données nettoyées et normalisées
  - **tables/** : Données transformées prêtes pour l'analyse

**Critères d'acceptation** :
- ✅ Storage Account Azure configuré (Data Lake Gen2)
- ✅ Containers séparés par couche (raw, cleaned, tables)
- ✅ Authentification sécurisée via Service Principal
- ✅ Rôles RBAC configurés (Storage Blob Data Contributor)

#### **Bloc 3 : Transformation ETL**
- Pipeline de **nettoyage automatique** :
  - Suppression lignes vides/doublons
  - Normalisation des en-têtes (minuscules, underscores)
  - Conversion UTF-8 standard
- Pipeline **Azure Data Factory** (si temps disponible) :
  - Conversion CSV → Parquet pour optimisation
  - Déclenchement automatique via Blob Storage Events

**Critères d'acceptation** :
- ✅ Script Python de nettoyage fonctionnel
- ✅ Workflow GitHub Actions pour le déplacement raw → cleaned
- ✅ Tests unitaires sur le nettoyage (pytest)

#### **Bloc 4 : Import en Base SQL Azure**
- Tables créées dynamiquement à partir des CSV nettoyés :
  - `ips_lycee` : Données IPS des lycées
  - `bac_par_academie` : Résultats du bac
  - `ecoles_effectifs` : Effectifs des écoles
  - `effectifs_tg` : Effectifs terminales générales
- Procédures de **conformité RGPD** :
  - Registre des traitements
  - Procédures de suppression automatique

**Critères d'acceptation** :
- ✅ Base SQL Azure opérationnelle (serveur + base de données)
- ✅ Scripts d'import modulaires (un script par table)
- ✅ Import automatisé via GitHub Actions
- ✅ Documentation des procédures RGPD

#### **Bloc 5 : API REST pour Mise à Disposition**
- API **FastAPI** avec endpoints :
  - `GET /api/ips_lycee` : Liste tous les lycées
  - `GET /api/ips_lycee/{code}` : Détails d'un lycée
  - `POST /api/auth/register` : Inscription utilisateur
  - `POST /api/auth/login` : Authentification JWT
- **Sécurité** :
  - Authentification JWT (tokens avec expiration)
  - Mots de passe hashés (bcrypt)
- **Documentation** :
  - OpenAPI/Swagger intégré
  - README avec exemples d'utilisation

**Critères d'acceptation** :
- ✅ API déployée sur Azure Container Instance
- ✅ Endpoints fonctionnels et sécurisés
- ✅ Documentation Swagger accessible
- ✅ Tests d'intégration des endpoints

#### **Bloc 6 : CI/CD et Déploiement**
- **GitHub Actions** pour automatisation :
  - Tests unitaires (pytest) à chaque push
  - Analyse qualité du code (black, isort, pylint)
  - Build et push Docker Hub
  - Déploiement automatique Azure Container Instance
- **Terraform** pour Infrastructure as Code :
  - Provisionning automatique des ressources Azure
  - Backend distant pour l'état Terraform

**Critères d'acceptation** :
- ✅ Workflows CI/CD fonctionnels
- ✅ Tests automatisés (couverture > 80%)
- ✅ Déploiement automatique réussi
- ✅ Infrastructure reproductible (Terraform)

### 1.3.2 Fonctionnalités Exclues du MVP (Version 2)

Les fonctionnalités suivantes sont identifiées mais **reportées à une version ultérieure** :
- 🔜 Interface web de visualisation (dashboard Power BI/Streamlit)
- 🔜 Exports automatiques en formats multiples (Excel, Parquet)
- 🔜 Système d'alertes par email (nouveaux datasets)
- 🔜 Gestion avancée des permissions (rôles granulaires)
- 🔜 Pipeline temps réel (streaming avec Azure Event Hub)
- 🔜 Data Quality checks automatiques (Great Expectations)

---

## 1.4 ÉTUDE DE FAISABILITÉ

### 1.4.1 Analyse des Opportunités

#### **Synthèse des Entretiens Métier**

**Partie prenante 1 : Analystes de données éducatives**
- **Besoin** : Accès rapide aux données pour analyses comparatives
- **Contrainte** : Temps perdu à chercher et nettoyer les données manuellement
- **Opportunité** : API centralisée réduirait ce temps de 80%

**Partie prenante 2 : Chefs d'établissements**
- **Besoin** : Comparaison de leur établissement avec des établissements similaires (IPS, résultats)
- **Contrainte** : Données dispersées sur plusieurs sites gouvernementaux
- **Opportunité** : Dashboard unifié améliorerait le pilotage

**Partie prenante 3 : Chercheurs en sciences de l'éducation**
- **Besoin** : Accès programmatique (Python/R) aux données pour recherche
- **Contrainte** : Formats hétérogènes, absence d'API
- **Opportunité** : API REST standardisée faciliterait la recherche reproductible

#### **Benchmark des Solutions Existantes**

| Solution | Points Forts | Points Faibles | Pertinence |
|----------|--------------|----------------|------------|
| **data.gouv.fr** | Données officielles, mises à jour régulières | Pas d'API unifiée, formats hétérogènes, pas de transformation | ⭐⭐⭐ Source de données |
| **OpenDataSoft** | API REST disponible, données Île-de-France | Périmètre géographique limité, pagination complexe | ⭐⭐ Complément |
| **INSEE** | Données financières officielles | Formats Excel complexes, pas d'API | ⭐⭐⭐ Source de données |
| **Solutions SaaS** (ex: DataGouv Plus) | Clé en main, support client | Coût élevé (>10k€/an), vendor lock-in | ⭐ Trop coûteux |
| **Data Hub custom** (notre solution) | Sur-mesure, maîtrise totale, faible coût | Développement interne nécessaire | ⭐⭐⭐⭐⭐ **Retenu** |

**Conclusion** : Aucune solution existante ne répond complètement au besoin. Un développement custom est justifié.

### 1.4.2 Analyse RICE (Priorisation)

| Critère | Valeur | Justification |
|---------|--------|---------------|
| **Reach** (Portée) | **1000** utilisateurs potentiels/an | • 100+ établissements scolaires région<br>• 50+ chercheurs en éducation<br>• 850+ analystes collectivités/académies |
| **Impact** (Impact) | **3** (élevé) | • Réduction 80% du temps d'accès aux données<br>• Amélioration prise de décision établissements<br>• Facilitation recherche académique |
| **Confidence** (Confiance) | **80%** | • Technologies éprouvées (Azure, Python, FastAPI)<br>• Sources de données stables<br>• Compétences disponibles<br>• Risque technique faible |
| **Effort** (Effort) | **6** semaines-personne | • 1 data engineer temps plein<br>• 3 sprints de 2 semaines<br>• Complexité moyenne |

**Score RICE = (Reach × Impact × Confidence) / Effort**

**Score RICE = (1000 × 3 × 0.8) / 6 = 400**

🟢 **Score > 100 → Projet hautement prioritaire et viable**

### 1.4.3 Rapport Objectifs / Qualité / Coûts / Délais

#### **Objectifs vs Moyens Disponibles**

| Dimension | Objectif | Moyens Disponibles | Alignement |
|-----------|----------|-------------------|------------|
| **Fonctionnel** | 5 sources intégrées + API | Sources publiques accessibles | ✅ Aligné |
| **Technique** | Data Lake + SQL + API | Azure (compte existant) | ✅ Aligné |
| **Humain** | Développement complet | 1 data engineer expérimenté | ✅ Aligné |
| **Temporel** | MVP en 6 semaines | Planning réaliste 3 sprints | ✅ Aligné |
| **Financier** | Coûts Azure maîtrisés | Budget < 100€/mois | ✅ Aligné |

#### **Qualité Attendue**

- **Fiabilité** : Tests unitaires > 80% couverture, CI/CD automatisé
- **Performance** : Temps de réponse API < 500ms pour 90% des requêtes
- **Sécurité** : Authentification JWT, secrets Azure Key Vault, HTTPS
- **Maintenabilité** : Code documenté, architecture modulaire, IaC (Terraform)
- **Accessibilité** : Documentation française, API RESTful standard

#### **Coûts Estimés**

| Poste | Détail | Coût Mensuel |
|-------|--------|--------------|
| **Azure Storage** | Data Lake Gen2 (50 GB) | ~5€ |
| **Azure SQL Database** | Basic tier (2 GB) | ~5€ |
| **Azure Container Instance** | 1 vCPU, 1.5 GB RAM | ~30€ |
| **Azure Data Factory** | Pipelines (usage faible) | ~10€ |
| **GitHub Actions** | 2000 min/mois (gratuit) | 0€ |
| **Docker Hub** | Repository public (gratuit) | 0€ |
| **Total** | | **~50€/mois** |

**Coût total 6 semaines** : ~75€ (1.5 mois)  
**ROI estimé** : Économie > 100h de travail manuel/an pour les utilisateurs

#### **Délais et Jalons**

| Jalon | Date | Livrables |
|-------|------|-----------|
| **J0 - Kick-off** | Semaine 1 | Réunion de lancement, backlog priorisé |
| **J14 - Fin Sprint 1** | Semaine 2 | Infrastructure + Scripts d'import |
| **J28 - Fin Sprint 2** | Semaine 4 | Pipeline ETL + Base SQL |
| **J42 - Fin Sprint 3** | Semaine 6 | API déployée + Documentation |
| **J45 - Recette** | Semaine 6 | Tests utilisateurs, corrections mineures |
| **J50 - Go Live** | Semaine 7 | Mise en production, communication |

---

## 1.5 CADRAGE PROJET ET HYPOTHÈSES TECHNIQUES

### 1.5.1 Hypothèses Techniques Macro

#### **Architecture Globale**

```
┌─────────────────────────────────────────────────────────────────┐
│                      SOURCES DE DONNÉES                          │
│  data.gouv.fr  │  INSEE  │  OpenDataSoft  │  (Futures sources) │
└────────────┬────────────────────────────────────────────────────┘
             │
             │ GitHub Actions (Imports mensuels automatisés)
             ▼
┌─────────────────────────────────────────────────────────────────┐
│              AZURE DATA LAKE GEN2 (Storage Account)             │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐                      │
│  │   raw/   │→ │ cleaned/ │→ │  tables/ │  (Architecture       │
│  │  Brut    │  │ Nettoyé  │  │Transformé│   Medallion)         │
│  └──────────┘  └──────────┘  └──────────┘                      │
└────────────┬────────────────────────────────────────────────────┘
             │
             │ GitHub Actions (Nettoyage + Import SQL)
             ▼
┌─────────────────────────────────────────────────────────────────┐
│                    AZURE SQL DATABASE                            │
│  Tables: ips_lycee | bac_par_academie | ecoles_effectifs |...  │
└────────────┬────────────────────────────────────────────────────┘
             │
             │ Connexion sécurisée (ODBC Driver 18)
             ▼
┌─────────────────────────────────────────────────────────────────┐
│         API REST (FastAPI) - Azure Container Instance           │
│  Endpoints: /ips_lycee | /auth/login | /auth/register          │
│  Sécurité: JWT Authentication | CORS | HTTPS                    │
└────────────┬────────────────────────────────────────────────────┘
             │
             │ HTTPS / JSON
             ▼
┌─────────────────────────────────────────────────────────────────┐
│                     UTILISATEURS FINAUX                          │
│  Analystes  │  Chercheurs  │  Applications  │  Dashboards       │
└─────────────────────────────────────────────────────────────────┘
```

#### **Stack Technique Sélectionnée**

| Composant | Technologie | Justification |
|-----------|-------------|---------------|
| **Langage** | Python 3.10+ | Écosystème data riche, lisibilité, expérience équipe |
| **Cloud** | Microsoft Azure | Compte existant, intégration native services, RGPD EU |
| **Storage** | Azure Data Lake Gen2 | Support formats multiples, scalabilité, coût optimisé |
| **Database** | Azure SQL Database | Requêtes relationnelles, compatibilité outils BI |
| **ETL** | Python (pandas) + Azure Data Factory | Flexibilité Python, orchestration ADF (optionnel) |
| **API** | FastAPI | Performance, documentation auto (OpenAPI), async |
| **Authentification** | JWT (python-jose) | Standard industrie, stateless, scalable |
| **CI/CD** | GitHub Actions | Gratuit, intégration native GitHub, workflows YAML |
| **Conteneurisation** | Docker | Reproductibilité, portabilité, déploiement simplifié |
| **IaC** | Terraform | State management, reproductibilité, version control |
| **Tests** | pytest | Standard Python, couverture, mocking |
| **Qualité Code** | black, isort, pylint | Formatage auto, imports, analyse statique |

### 1.5.2 Représentations Architecturales

#### **Représentation Fonctionnelle**

**Cas d'usage principaux** :
1. **Import automatique** : Le système récupère les données des sources publiques mensuellement
2. **Nettoyage** : Les données brutes sont normalisées et validées
3. **Stockage structuré** : Les données sont organisées en couches (raw/cleaned/tables)
4. **Requêtage SQL** : Les analystes interrogent directement la base SQL
5. **Accès API** : Les applications consomment les données via endpoints REST
6. **Authentification** : Les utilisateurs s'inscrivent et s'authentifient via JWT

#### **Représentation Applicative - Matrice des Flux**

| Source | Destination | Fréquence | Volume | Format | Protocole |
|--------|-------------|-----------|--------|--------|-----------|
| data.gouv.fr | Storage raw/ | Mensuel | ~10 MB | CSV | HTTPS GET |
| INSEE | Storage raw/ | Mensuel | ~2 MB | XLSX | HTTPS GET |
| OpenDataSoft | Storage raw/ | Mensuel | ~5 MB | JSON | REST API |
| Storage raw/ | Storage cleaned/ | Quotidien | ~15 MB | CSV | Azure SDK |
| Storage cleaned/ | SQL Database | Hebdomadaire | ~15 MB | CSV → SQL | pyodbc |
| SQL Database | API FastAPI | Temps réel | Variable | SQL → JSON | ODBC |
| API | Utilisateurs | Temps réel | <1 MB/req | JSON | HTTPS REST |

#### **Représentation d'Infrastructure**

**Ressources Azure provisionnées** :

```yaml
Resource Group: RG-OKOTWICA-Prod
├── Storage Account: sadatalakeokotwicaprod
│   ├── Container: raw
│   ├── Container: cleaned
│   └── Container: tables
├── SQL Server: sqlserver-okotwica-prod
│   └── Database: EducationData (Basic, 2GB)
├── Data Factory: adf-okotwica-prod (optionnel)
├── Container Instance: education-api-container
│   ├── Image: olaffsen/education-api:latest
│   ├── CPU: 1 vCPU
│   ├── Memory: 1.5 GB
│   └── Port: 8000 (HTTPS)
└── Key Vault: kv-okotwica-secrets (futur)
```

**Réseau et sécurité** :
- 🔒 Authentification : Service Principal (CI/CD) + JWT (utilisateurs)
- 🔒 Secrets : Variables GitHub Secrets (migration Key Vault prévue)
- 🔒 Firewall : SQL autorise uniquement Azure services
- 🔒 HTTPS : Obligatoire pour l'API (TLS 1.2+)

#### **Représentation Opérationnelle**

**Flux opérationnels automatisés** :

```mermaid
graph LR
    A[1er du mois 00:00 UTC] -->|Trigger| B[GitHub Actions: Import]
    B --> C{Fichiers existent?}
    C -->|Non| D[Téléchargement sources]
    C -->|Oui| E[Skip import]
    D --> F[Upload Storage raw/]
    F --> G[Trigger: Nettoyage]
    G --> H[Storage cleaned/]
    H --> I[Trigger: Import SQL]
    I --> J[Tables SQL mises à jour]
    J --> K[API sert nouvelles données]
```

### 1.5.3 Conformité RGPD

#### **Analyse des Données Personnelles**

**Données collectées** :
- ❌ **Aucune donnée personnelle identifiante** dans les datasets publics (IPS, effectifs, résultats agrégés)
- ✅ **Données d'établissements** : UAI (identifiant), nom, commune (données publiques)
- ⚠️ **Utilisateurs API** : email, nom, prénom, mot de passe hashé (bcrypt)

**Registre des traitements** :

| Traitement | Finalité | Base légale | Durée conservation | Mesures sécurité |
|------------|----------|-------------|-------------------|------------------|
| Collecte données publiques | Centralisation analyse | Intérêt légitime | Indéfinie (publique) | Chiffrement transit |
| Stockage utilisateurs | Authentification API | Consentement | 3 ans inactivité | Hash bcrypt, JWT |
| Logs d'accès | Sécurité, debug | Intérêt légitime | 90 jours | Accès restreint |

**Actions de mise en conformité** :

1. **Minimisation** : Collecte uniquement des données nécessaires (pas de tracking)
2. **Sécurisation** :
   - Mots de passe hashés avec bcrypt (salt automatique)
   - Tokens JWT avec expiration (30 min par défaut)
   - Connexions HTTPS obligatoires
3. **Transparence** :
   - Politique de confidentialité accessible (documentation API)
   - Informations claires sur les données collectées
4. **Droits utilisateurs** :
   - Droit d'accès : Endpoint `GET /api/users/me` (futur)
   - Droit de suppression : Endpoint `DELETE /api/users/me` (futur)
   - Droit de rectification : Endpoint `PATCH /api/users/me` (futur)
5. **Procédures automatisées** :
   - Script de suppression comptes inactifs > 3 ans (cronjob)
   - Purge logs > 90 jours (Azure Policy)

### 1.5.4 Stratégie d'Éco-responsabilité

**Référentiel appliqué** : Référentiel général d'éco-conception des services numériques (RGESN)

#### **Actions mises en œuvre**

| Dimension | Action | Impact estimé |
|-----------|--------|---------------|
| **Hébergement** | Choix région Azure France Central (électricité bas carbone) | -30% empreinte carbone |
| **Architecture** | Architecture serverless (Container Instance) → scaling automatique | -40% ressources inutilisées |
| **Stockage** | Formats optimisés (Parquet > CSV) pour réduire taille | -60% espace stockage |
| **Code** | Requêtes SQL optimisées (index, jointures) | -20% temps calcul |
| **Réseau** | Compression JSON (gzip) sur réponses API | -50% bande passante |
| **Cycle de vie** | Conservation 3 ans max données personnelles | Réduction volume stocké |

**Choix des prestataires** :
- ✅ Microsoft Azure : Objectif neutralité carbone 2030
- ✅ GitHub : Infrastructure verte (100% énergies renouvelables datacenter)

### 1.5.5 Accessibilité du Projet

#### **Accessibilité de la Réalisation**

**Postes de travail équipe technique** :
- 🖥️ Environnements de développement locaux (VS Code)
- 🖥️ Accès GitHub (interface web + CLI git)
- 🖥️ Accès Azure Portal (interface web accessible)

**Adaptations prévues** : Aucune adaptation spécifique nécessaire (équipe sans contraintes identifiées)

#### **Accessibilité des Livrables**

**Documentation technique** :
- ✅ README.md en français, structure claire (titres hiérarchisés)
- ✅ Diagrammes avec alternatives textuelles (descriptions Mermaid)
- ✅ Code commenté en français
- ✅ Noms de variables explicites (pas d'abbréviations obscures)

**API** :
- ✅ Documentation OpenAPI/Swagger (interface visuelle)
- ✅ Messages d'erreur clairs et explicites
- ✅ Endpoints RESTful standards (GET, POST, DELETE)
- ✅ Exemples de requêtes dans la documentation

**Conformité visée** : Recommandations RGAA niveau AA (documentation textuelle)

---

## 1.6 RISQUES ET STRATÉGIES D'ATTÉNUATION

### 1.6.1 Matrice des Risques

| Risque | Probabilité | Impact | Criticité | Stratégie d'atténuation |
|--------|-------------|--------|-----------|-------------------------|
| **R1 : Changement structure données sources** | Moyenne | Élevé | 🟠 Majeur | • Tests unitaires des parsers<br>• Alertes sur échecs imports<br>• Documentation des formats attendus |
| **R2 : Dépassement délai** | Faible | Moyen | 🟡 Modéré | • Priorisation stricte (MoSCoW)<br>• Sprint reviews régulières<br>• Buffer 20% dans planning |
| **R3 : Dépassement budget Azure** | Faible | Faible | 🟢 Mineur | • Alertes Azure Cost Management (>40€/mois)<br>• Choix tiers faibles coûts (Basic SQL) |
| **R4 : Faille sécurité API** | Faible | Élevé | 🟠 Majeur | • Authentification JWT obligatoire<br>• Sanitization inputs (paramètres SQL)<br>• Tests sécurité (OWASP Top 10) |
| **R5 : Perte compétence clé** | Très faible | Élevé | 🟡 Modéré | • Documentation exhaustive<br>• Code review systématique<br>• Partage connaissances (README) |
| **R6 : Indisponibilité GitHub Actions** | Faible | Moyen | 🟡 Modéré | • Imports scriptés (exécution locale possible)<br>• Logs persistants Azure Storage |

### 1.6.2 Plan de Contingence

**Scénario critique** : Changement majeur format data.gouv.fr pendant le projet

**Actions immédiates** :
1. ⏸️ Pause des imports automatiques
2. 🔍 Analyse du nouveau format (échantillon)
3. 🛠️ Adaptation des parsers (sprint dédié si nécessaire)
4. ✅ Tests sur données historiques
5. ▶️ Reprise imports

**Ressources mobilisables** : +1 semaine de développement (buffer)

---

## 1.7 SYNTHÈSE DE L'AVANT-PROJET

### 1.7.1 Décision de Lancement

✅ **Faisabilité confirmée