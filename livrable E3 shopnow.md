# E3 — Lancement d'un projet data
## Support de présentation & documents associés — ShopNow Marketplace
### Certification RNCP 37638 — Expert en données massives (Data Engineer)

---

**Candidat(e)** : Olive  
**Formation** : Expert en infrastructures de données massives — Simplon  
**Projet** : ShopNow Marketplace — Transformation e-commerce vers marketplace multi-vendeurs  
**Date de rédaction** : Février 2026  
**Version** : 1.0

---

## Table des matières

1. Avant-projet — Synthèse (rappel E2)
2. Feuille de route (Roadmap) du projet
3. Calendrier de production et attribution des ressources
4. Méthode de suivi et outils associés
5. Stratégie de communication interne
6. Simulation — Script de l'introduction de la réunion de lancement

---

## 1. Avant-projet — Synthèse (rappel issu de E2)

> *Ce document prolonge directement le rapport professionnel E2. Les éléments ci-dessous sont les points clés à présenter au jury lors de la réunion de lancement.*

### Contexte rappelé

ShopNow transforme sa plateforme e-commerce en marketplace multi-vendeurs. Cette transformation génère un besoin urgent de centralisation et d'exploitation des données sur Azure. L'entrepôt de données (Azure Synapse Analytics) alimenté par des pipelines ETL (Azure Data Factory) est le cœur du projet.

### Hypothèses techniques retenues

- **Architecture** : Medallion (Bronze / Silver / Gold) sur Azure Data Lake Storage Gen2 + Azure Synapse Analytics
- **Orchestration ETL** : Azure Data Factory
- **Visualisation** : Power BI Premium Per User
- **IAC** : Terraform pour le déploiement de l'infrastructure
- **Méthodologie** : Agile Scrum — sprints de 2 semaines

### Préconisations macro

| Axe | Préconisation |
|---|---|
| RGPD | Pseudonymisation dès l'ingestion en zone Bronze — registre des traitements dès J+15 |
| Accessibilité | Rapports Power BI conformes RGAA dès la première livraison |
| Éco-responsabilité | Synapse Auto-Pause activé — Tier de stockage intelligent sur ADLS |
| Risque qualité données | Règles de validation ADF + quarantaine automatique des données incorrectes |

---

## 2. Feuille de route (Roadmap) du projet

La roadmap est découpée en **4 grandes étapes** correspondant aux ensembles fonctionnels du projet.

```
ROADMAP — ShopNow Data Warehouse
═══════════════════════════════════════════════════════════════════════
 ÉTAPE 1         ÉTAPE 2          ÉTAPE 3           ÉTAPE 4
 Fondations      Collecte &       Modélisation &    Mise en
 & Gouvernance   Ingestion        Transformation    Production
 ──────────      ──────────       ──────────────    ──────────
 J0 → J+30       J+15 → J+60      J+45 → J+75       J+75 → J+90
 
 Sprint 1-2      Sprint 2-4       Sprint 4-6        Sprint 6
═══════════════════════════════════════════════════════════════════════
```

### Étape 1 — Fondations & Gouvernance (J0 → J+30)

**Objectif** : Mettre en place l'infrastructure Azure, documenter la gouvernance des données et démarrer la conformité RGPD.

| Livrable | Responsable | Échéance |
|---|---|---|
| Infrastructure Azure déployée (Terraform) | Data Engineer | J+15 |
| Registre des traitements RGPD v1 | DPO + Data Engineer | J+20 |
| Accès et habilitations configurés (RBAC Azure AD) | Data Engineer | J+20 |
| Documentation architecture technique v1 | Data Engineer | J+30 |
| Glossaire métier v1 validé | Data Analyst + Référents métier | J+30 |

**Risques identifiés** : Délai d'approvisionnement des licences Azure — anticipé par une demande DSI dès J-15.

---

### Étape 2 — Collecte & Ingestion (J+15 → J+60)

**Objectif** : Développer et déployer l'ensemble des pipelines ETL vers la zone Bronze.

| Livrable | Responsable | Échéance |
|---|---|---|
| Pipeline SQL Server → ADLS Bronze | Data Engineer | J+30 |
| Pipeline Azure SQL CRM → ADLS Bronze | Data Engineer | J+35 |
| Pipeline API REST Vendeurs → ADLS Bronze | Data Engineer | J+45 |
| Pipeline CSV/Blob → ADLS Bronze | Data Engineer | J+50 |
| Tests d'intégration pipelines | Data Engineer + Data Analyst | J+60 |
| Monitoring ADF + alertes configurées | Data Engineer | J+55 |

**Risques identifiés** : API vendeurs sans documentation officielle — 3 jours de buffer prévu pour le reverse engineering.

---

### Étape 3 — Modélisation & Transformation (J+45 → J+75)

**Objectif** : Construire le modèle dimensionnel, développer les transformations Silver et Gold, créer les premiers tableaux de bord.

| Livrable | Responsable | Échéance |
|---|---|---|
| Modèle dimensionnel (schéma en étoile) validé | Data Analyst + Référents métier | J+50 |
| Transformations Bronze → Silver (nettoyage, déduplication) | Data Engineer | J+60 |
| Transformations Silver → Gold (agrégations, dimensions) | Data Engineer + Data Analyst | J+70 |
| 3 premiers tableaux de bord Power BI (ventes, vendeurs, finance) | Data Analyst | J+75 |
| Recette fonctionnelle avec les équipes métier | Tous | J+75 |

**Risques identifiés** : Désaccord sur la définition des KPI — atelier de co-définition prévu dès J+45 avec Finance et Commerce.

---

### Étape 4 — Mise en production (J+75 → J+90)

**Objectif** : Déployer en production, former les utilisateurs, livrer la documentation finale.

| Livrable | Responsable | Échéance |
|---|---|---|
| Déploiement production (Terraform apply) | Data Engineer | J+80 |
| 2 tableaux de bord supplémentaires (logistique, conformité) | Data Analyst | J+82 |
| Documentation utilisateur Power BI (accessible RGAA) | Data Analyst | J+85 |
| Sessions de formation utilisateurs (4 équipes × 2h) | Data Analyst + Chef de projet | J+85 à J+88 |
| Bilan de projet + REX documenté | Chef de projet | J+90 |

---

## 3. Calendrier de production et attribution des ressources

### 3.1 Équipe projet et compétences

| Membre | Rôle | Compétences clés | Taux d'affectation |
|---|---|---|---|
| Olive | Data Engineer (lead) | Azure (ADF, Synapse, ADLS), Terraform, Python, SQL | 100% |
| [Prénom] | Data Analyst | Power BI DAX, modélisation dimensionnelle, SQL | 80% |
| [Prénom] | DPO | RGPD, registre des traitements, droit numérique | 20% |
| [Prénom] | Chef de projet DSI | Pilotage Agile, coordination métier, budget | 50% |
| Référents métier (×3) | Finance / Commerce / Logistique | Validation fonctionnelle, recette | 10% chacun |

*Note accessibilité : les postes de travail de l'équipe technique sont équipés de doubles écrans et configurés pour le développement distant sur Azure. Aucun aménagement spécifique de poste n'a été identifié comme nécessaire à ce stade — à réévaluer si un nouveau membre rejoint l'équipe.*

### 3.2 Planning détaillé — Diagramme de Gantt (synthèse)

```
SPRINT        S1    S2    S3    S4    S5    S6
SEMAINE       1-2   3-4   5-6   7-8   9-10  11-12
──────────────────────────────────────────────────────────────────
Infrastructure[████]
RGPD / Gouv.  [████][██  ]
Pipeline SQL  [    ][████]
Pipeline CRM        [████]
Pipeline API              [████]
Pipeline CSV              [    ][██  ]
Tests ETL                       [████]
Monitoring          [    ][    ][████]
Modèle DW                 [    ][████]
Transfo Silver                        [████]
Transfo Gold                          [    ][████]
PBI Dashboards                              [████][████]
Formation                                         [████]
Documentation [    ][    ][    ][    ][    ][████]
──────────────────────────────────────────────────────────────────
```

### 3.3 Pondération des efforts — Méthode de l'unité équivalente

La pondération des tâches a été réalisée collectivement lors du sprint 0 selon la **méthode de l'unité équivalente** (1 UE = 1 jour/homme de référence) :

| Tâche | UE estimées | Ressource |
|---|---|---|
| Déploiement infrastructure Terraform | 5 UE | Data Engineer |
| Pipeline SQL Server (ingestion) | 4 UE | Data Engineer |
| Pipeline API REST Vendeurs | 6 UE | Data Engineer (complexité API) |
| Modèle dimensionnel | 5 UE | Data Analyst |
| Transformations Silver/Gold | 8 UE | Data Engineer + Data Analyst |
| Tableaux de bord Power BI (5) | 10 UE | Data Analyst |
| Documentation + formation | 4 UE | Data Analyst + Chef de projet |
| Conformité RGPD | 3 UE | DPO + Data Engineer |
| **TOTAL** | **45 UE** | |

*Les éléments de planification ont été partagés avec l'équipe via Azure DevOps (accessible, compatibilité lecteur d'écran NVDA validée) et sous forme de document PDF balisé conforme RGAA, conformément aux recommandations de l'association Valentin Haüy.*

---

## 4. Méthode de suivi et outils associés

### 4.1 Outils de suivi

| Outil | Usage | Accès |
|---|---|---|
| **Azure DevOps** | Backlog Scrum, Kanban board, Burndown chart | Toute l'équipe — authentification Azure AD |
| **Azure Monitor** | Supervision des pipelines ETL, alertes en cas d'erreur | Data Engineer (alertes email pour tous) |
| **Confluence** | Documentation projet, comptes-rendus, REX | Toute l'équipe + parties prenantes métier |
| **Teams** | Communication quotidienne, dailys asynchrones | Toute l'équipe |
| **Power BI Service** | Suivi des indicateurs projet (vélocité, bugs) | Chef de projet + Management |

### 4.2 Rituels Agile Scrum

| Rituel | Fréquence | Durée | Participants | Format |
|---|---|---|---|---|
| **Daily standup** | Chaque jour ouvré | 15 min | Équipe technique | Présentiel ou Teams |
| **Sprint planning** | Début de chaque sprint (J0, J+14, J+28…) | 2h | Équipe + Chef de projet | Présentiel |
| **Sprint review** | Fin de chaque sprint | 1h | Équipe + Référents métier | Présentiel — démo en direct |
| **Rétrospective** | Fin de chaque sprint | 45 min | Équipe technique | Présentiel |
| **Comité de pilotage** | Mensuel | 1h | Chef de projet + Direction + DPO | Présentiel |

**Règles de participation** :
- Le daily standup est asynchrone en cas d'impossibilité (message Teams structuré : fait hier / prévu aujourd'hui / blocages)
- Les comptes-rendus de sprint review sont rédigés dans Confluence dans les 24h et partagés à toutes les parties prenantes
- Toute décision d'arbitrage est documentée dans la section "Décisions" du Confluence du projet

### 4.3 Indicateurs de suivi

| Indicateur | Cible | Outil de mesure | Fréquence |
|---|---|---|---|
| **Vélocité sprint** | ≥ 7 UE / sprint | Azure DevOps Burndown | Par sprint |
| **Taux de completion du backlog** | 100% des user stories priorité 1 | Azure DevOps Board | Par sprint |
| **Taux de succès pipelines ETL** | ≥ 98% | Azure Monitor | Quotidien |
| **Taux de fraîcheur des données** | Données disponibles en ≤ 1h | Azure Monitor | Continu |
| **Couverture RGPD** | 100% des traitements documentés | Registre DPO | Mensuel |
| **Budget consommé** | ≤ 100% du budget prévu | Azure Cost Management | Hebdomadaire |

---

## 5. Stratégie de communication interne

### 5.1 Plan de communication — Vue d'ensemble

| Étape | Type de communication | Public cible | Format | Responsable | Échéance |
|---|---|---|---|---|---|
| **Lancement** | Réunion de kick-off | Toute l'équipe + Direction | Présentiel — slides | Chef de projet | J0 |
| **Fin sprint 1** | Note d'avancement | Direction + DSI | Email + document Confluence | Chef de projet | J+14 |
| **Fin sprint 2** | Démo intermédiaire (pipelines) | Référents métier + DSI | Teams — démo live | Data Engineer | J+28 |
| **Jalon Bronze** | Rapport d'étape v1 | Direction + DPO | Document PDF accessible | Chef de projet | J+30 |
| **Fin sprint 3** | Note d'avancement | Direction + DSI | Email + Confluence | Chef de projet | J+42 |
| **Fin sprint 4** | Démo modèle de données | Référents métier | Présentiel — démo Synapse | Data Analyst | J+56 |
| **Jalon Silver** | Rapport d'étape v2 | Direction + DPO | Document PDF accessible | Chef de projet | J+60 |
| **Recette fonctionnelle** | Ateliers de validation | Équipes métier (×4) | Présentiel — 2h par équipe | Data Analyst | J+75 |
| **Livraison finale** | Présentation de lancement production | Direction + toutes équipes | Présentiel — slides + démo | Chef de projet + Data Analyst | J+90 |
| **Post-lancement** | Enquête de satisfaction | Toutes équipes utilisatrices | Formulaire Teams | Chef de projet | J+97 |

### 5.2 Orientations et arbitrages communiqués

Chaque communication présentera explicitement :
- Les **choix techniques retenus** et leur justification (ex : pourquoi Synapse plutôt que Databricks, pourquoi batch plutôt que streaming)
- Les **arbitrages menés** face aux contraintes (ex : priorisation des tables sources, report de la fonctionnalité logs temps réel en phase 2)
- Les **impacts pour les équipes** concernées (ex : changement de processus, nouvelles interfaces à utiliser)

### 5.3 Documentation utilisateur et accompagnement

| Action | Responsable | Échéance | Format |
|---|---|---|---|
| Guide utilisateur Power BI (5 tableaux de bord) | Data Analyst | J+85 | PDF balisé accessible (RGAA) — police ≥ 12pt, contraste 4,5:1 |
| FAQ technique (pipelines, accès, droits) | Data Engineer | J+85 | Confluence — navigation par ancres |
| Sessions de formation (4 équipes × 2h) | Data Analyst + Chef de projet | J+85→J+88 | Présentiel — supports projetés + PDF distribué |
| Vidéo de prise en main Power BI (5 min) | Data Analyst | J+88 | Sous-titrée — hébergée sur Teams |

Les sessions de formation sont adaptées aux besoins de chaque équipe (Finance ≠ Logistique ≠ Marketing). Les participants sont ceux dont les missions sont directement concernées par la prise en main des tableaux de bord livrés.

### 5.4 Recueil des retours et traitement

Le recueil des retours des parties prenantes suit un processus intégré à la stratégie de communication :

1. **Sprint review** : retours fonctionnels collectés en direct — consignés dans Confluence dans les 24h
2. **Formulaire de satisfaction** post-lancement (J+97) : dépouillement en J+100 — synthèse partagée à la Direction
3. **Canal Teams dédié** (#shopnow-data-feedback) : ouvert dès J0 — traité hebdomadairement par le chef de projet
4. **Ticket Azure DevOps** : tout retour de bug ou d'amélioration est tracé, priorisé et assigné dans le backlog

---

## 6. Simulation — Script de l'introduction de la réunion de lancement

> *Ce script correspond à la présentation orale de 10 minutes simulée devant le jury (E3). Il suit la structure : accueil → contexte → équipe → roadmap → méthode de travail → communication → call to action.*

---

### [SLIDE 1 — Titre]

**"ShopNow Data Warehouse — Réunion de lancement"**
*Data Engineer lead : Olive — [Date]*

---

### [SLIDE 2 — Accueil et ordre du jour]

> *Bonjour à toutes et à tous. Je suis ravi(e) de vous accueillir pour le lancement officiel du projet data ShopNow. Cette réunion a pour objectif de nous aligner collectivement sur les objectifs du projet, l'organisation que nous allons mettre en place, et le rôle de chacun dans sa réussite.*

**Ordre du jour (10 min) :**
1. Rappel du contexte et des enjeux — 2 min
2. Présentation de l'équipe et des rôles — 1 min
3. Feuille de route et jalons clés — 3 min
4. Comment nous allons travailler ensemble — 2 min
5. Questions et prochaines étapes — 2 min

---

### [SLIDE 3 — Pourquoi ce projet, pourquoi maintenant ?]

> *ShopNow est en train de vivre une transformation majeure. Nous passons d'un e-commerce classique à une marketplace multi-vendeurs. Cette transformation, c'est une formidable opportunité commerciale — mais elle multiplie également par 5 le volume de données que nous allons gérer.*
>
> *Aujourd'hui, ces données sont fragmentées : les ventes sont dans SQL Server, les clients dans le CRM, les catalogues vendeurs dans des API partenaires, les logs dans Azure Blob. Personne n'a une vision consolidée. C'est ce problème que ce projet va résoudre.*
>
> *Dans 90 jours, nous aurons un entrepôt de données centralisé sur Azure Synapse, 5 tableaux de bord Power BI opérationnels, et une infrastructure conforme au RGPD. Ce n'est pas un projet "data pour la data" — c'est un projet stratégique pour que ShopNow puisse piloter sa croissance.*

**Objectif résumé en une phrase :**
> *"Centraliser, fiabiliser et rendre exploitables toutes les données de la marketplace, pour que chaque équipe puisse prendre de meilleures décisions, plus vite."*

---

### [SLIDE 4 — L'équipe projet]

> *Voici l'équipe qui va porter ce projet. Nous sommes intentionnellement une équipe resserrée pour rester agiles.*

| Rôle | Nom | Mission principale |
|---|---|---|
| Data Engineer lead | Olive | Architecture, ETL, infrastructure |
| Data Analyst | [Prénom] | Modèle de données, Power BI |
| DPO | [Prénom] | Conformité RGPD |
| Chef de projet | [Prénom] | Pilotage, coordination, budget |
| Référents métier | Finance, Commerce, Logistique | Validation fonctionnelle |

> *Je veux insister sur le rôle des référents métier. Vous n'êtes pas là juste pour valider en fin de parcours. Vous êtes des membres actifs de ce projet — vos retours à chaque sprint review seront déterminants pour que ce qu'on livre corresponde vraiment à vos besoins.*

---

### [SLIDE 5 — Feuille de route]

> *Voici comment nous allons progresser sur les 3 prochains mois.*

**4 grandes étapes :**

| Étape | Période | Ce qu'on livre |
|---|---|---|
| 🏗️ Fondations & Gouvernance | J0 → J+30 | Infrastructure Azure, accès, registre RGPD |
| 🔄 Collecte & Ingestion | J+15 → J+60 | Tous les pipelines ETL opérationnels |
| 🔧 Modélisation & Transformation | J+45 → J+75 | Modèle dimensionnel + 3 premiers dashboards |
| 🚀 Mise en production | J+75 → J+90 | Déploiement + formation + documentation |

> *Vous noterez que les étapes se chevauchent volontairement — pendant qu'on finalise les pipelines, on commence déjà à modéliser. Ça nous permet de livrer de la valeur progressivement plutôt que d'attendre la fin.*

**Premier jalon visible pour vous : J+28 — démo des premiers pipelines en fonctionnement.**

---

### [SLIDE 6 — Comment nous allons travailler ensemble]

> *Nous travaillons en Agile Scrum, par sprints de 2 semaines. Concrètement, qu'est-ce que ça change pour vous ?*

**Ce que ça implique pour l'équipe technique :**
- Un daily standup de 15 min chaque matin
- Un sprint planning tous les 14 jours pour prioriser
- Une sprint review en fin de sprint — avec une démonstration en live

**Ce que ça implique pour vous, les référents métier :**
- Environ 2h tous les 14 jours pour assister aux sprint reviews et valider les livrables
- La possibilité de faire remonter vos retours à tout moment via le canal Teams dédié

> *Tout est suivi dans Azure DevOps — backlog, avancement, bugs. Vous y aurez accès en lecture si vous souhaitez suivre l'avancement en temps réel.*

**Notre outil de communication central : le canal Teams #shopnow-data**
> *Toute décision importante, tout arbitrage, toute alerte y sera communiqué. Si vous recevez une information importante par un autre canal, merci de la relayer là pour que toute l'équipe soit alignée.*

---

### [SLIDE 7 — Ce que nous attendons de vous]

> *Pour que ce projet réussisse, nous avons besoin de trois choses de votre part.*

1. **Votre disponibilité aux sprint reviews** — 2h tous les 14 jours. C'est le moment où votre feedback a le plus de valeur.

2. **Votre réactivité sur les questions de définition** — Quand on vous demande "comment calculez-vous votre taux de marge ?", répondre en 48h nous évite parfois plusieurs jours de retard.

3. **Votre remontée proactive des problèmes** — Si vous voyez que quelque chose ne va pas dans un tableau de bord, dites-le immédiatement via Teams. Plus tôt on sait, plus facile c'est à corriger.

---

### [SLIDE 8 — Prochaines étapes]

> *Pour clôturer cette réunion de lancement, voici les actions immédiates.*

| Action | Responsable | Échéance |
|---|---|---|
| Validation des accès Azure DevOps | Chef de projet | Aujourd'hui |
| Invitation sprint review #1 | Chef de projet | Aujourd'hui |
| Confirmation des référents métier officiels | Chaque direction | J+3 |
| Kick-off technique (équipe data) | Data Engineer | Demain matin |
| Premier daily standup | Toute l'équipe | Demain 9h30 |

> *Une question avant de terminer : y a-t-il des contraintes calendaires importantes dans vos équipes sur les 3 prochains mois dont je devrais tenir compte — clôtures, congés, audits — que nous n'aurions pas encore identifiées ?*

> *Merci à tous. Nous avons 90 jours devant nous, une équipe solide, et un projet qui a du sens. Lançons-nous !*

---

*Document rédigé dans le cadre de la certification RNCP 37638 — Expert en données massives (Data Engineer) — Simplon*  
*Version 1.0 — Février 2026*

---

## Annexe — Checklist conformité accessibilité (livrables E3)

Conformément aux recommandations de l'association Valentin Haüy et du RGAA v4 :

| Critère | Statut |
|---|---|
| Contraste texte ≥ 4,5:1 sur tous les supports de présentation | ✅ |
| Titres structurés (H1/H2/H3) dans tous les documents | ✅ |
| Tableaux avec en-têtes de colonnes balisées | ✅ |
| L'information n'est jamais transmise par la couleur seule | ✅ |
| Documents PDF exportés avec balises de structure | ✅ |
| Vidéo de formation sous-titrée | ✅ (prévu J+88) |
| Navigation clavier opérationnelle sur Power BI Service | ✅ |
| Police ≥ 12pt sur tous les documents imprimables | ✅ |
