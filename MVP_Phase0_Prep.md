# Document de Préparation - Projet SAV Immobilier

**Projet :** Application de suivi des travaux de garantie en immobilier neuf  
**Version :** 0.1 (Draft)  
**Date :** Décembre 2025  
**Équipe :** Ugo (Développeur), Damien (Domaine expert)

---

## 1. VISION & CONTEXTE

### 1.1 Le Problème

**Situation actuelle :**
- Les demandes de travaux de SAV passent par des emails entre multiples acteurs (locataires, promoteurs, MOE, entreprises)
- Perte de temps significative et retards de chantier
- Manque de traçabilité et de coordination entre les différents intervenants
- Processus de signature et validation dispersés

**Impact métier :**
- Délais allongés pour résoudre les problèmes
- Mauvaise expérience client (locataires frustés)
- Inefficacité administrative pour les promoteurs/syndics

### 1.2 La Solution Proposée

Une **plateforme centralisée** qui :
- Permet aux locataires de signaler les problèmes via QR code
- Automatise le routage des demandes vers les bons intervenants
- Permet aux artisans de documenter les interventions (photos, description)
- Centralise les signatures et validations

### 1.3 Cible Utilisateurs (MVP)

| Rôle | Description | Nombre estimé |
|------|-------------|---------------|
| **Locataire/Propriétaire** | Signale les problèmes | Variable |
| **Artisan/Entreprise** | Effectue les travaux | 5-10 (MVP) |
| **Promoteur/Syndic** | Gère les demandes, administre la plateforme | 1-2 (MVP) |
| **Admin/Coordinateur** | Gestion globale (peut être Damien) | 1 |

---

## 2. OBJECTIFS DU MVP

### 2.1 Objectifs Primaires

- [ ] Permettre à un locataire de créer une demande via QR code en < 2 min
- [ ] Permettre à un artisan de scanner, documenter et signer une intervention
- [ ] Permettre à un admin/promoteur de suivre toutes les demandes en temps réel
- [ ] Automatiser le routage des demandes vers les bons intervenants

### 2.2 Objectifs Secondaires (à définir après feedback Damien)

- [ ] Intégration avec des prestataires existants
- [ ] Notifications/alertes en temps réel
- [ ] Export de rapports (données de suivi)

### 2.3 Ce qui N'EST PAS au MVP

- Signature légale conforme (juste signature numérique simple pour preuves)
- Intégration de devis/facturation
- Multi-langue
- App native iOS (web responsive suffit)

---

## 3. ANALYSE DES BESOINS

### 3.1 User Stories - LOCATAIRE

```
En tant que locataire d'un logement neuf,
Je veux pouvoir signaler rapidement un problème,
Afin que le problème soit traité rapidement par le promoteur/syndic.

Critères d'acceptation :
- Je scanne un QR code avec mon téléphone
- Je suis dirigé vers un formulaire pré-rempli avec mon adresse
- Je renseigne : type de problème, description, photos
- Je reçois une confirmation de création avec un numéro de demande
- Je peux suivre l'état de ma demande (en attente, en cours, résolu)
```

### 3.2 User Stories - ARTISAN/ENTREPRISE

```
En tant qu'artisan envoyé pour une intervention,
Je veux confirmer ma présence et documenter l'intervention,
Afin de laisser une trace de ce qui a été fait.

Critères d'acceptation :
- Je scanne un QR code sur place
- Je vois les détails de l'intervention à effectuer
- Je peux ajouter des photos avant/après
- Je peux faire signer le locataire sur l'écran du téléphone
- Les données sont enregistrées (offline-capable serait +)
```

### 3.3 User Stories - PROMOTEUR/SYNDIC/ADMIN

```
En tant que gestionnaire de syndic/promoteur,
Je veux centraliser toutes les demandes de mes logements,
Afin de coordonner les interventions efficacement.

Critères d'acceptation :
- Dashboard avec liste de toutes les demandes
- Filtres : état (nouveau, attribué, en cours, terminé), urgence, type
- Assigner une demande à une entreprise
- Voir l'historique complet de chaque demande
- Exporter un rapport (PDF/Excel)
- Gérer les entreprises/artisans autorisés
```

---

## 4. SPÉCIFICATIONS TECHNIQUES (À AFFINER)

### 4.1 Architecture Générale

```
┌─────────────────┐
│  Frontend Web   │  (Admin/Promoteur - Dashboard suivi)
│   (React/Vue)   │
└────────┬────────┘
         │
         ├─────────────────────────────────────┐
         │                                     │
┌────────▼────────┐              ┌─────────────▼──────┐
│  Frontend Mobile│              │   Backend API      │
│ (React Native   │              │  (Node/Python)     │
│  ou Web Mobile) │              │                    │
└────────┬────────┘              └─────────┬──────────┘
         │                                  │
         └──────────────────┬───────────────┘
                            │
                    ┌───────▼────────┐
                    │  Base de Données│
                    │   (PostgreSQL)  │
                    └────────────────┘
```

### 4.2 Stack Technologique (Proposé)

**À confirmer avec Damien, mais proposition :**

| Composant | Technologie | Justification |
|-----------|-------------|---------------|
| **Backend API** | Node.js + Express | Rapide à déployer, JS partout, écosystème riche |
| **Base de Données** | PostgreSQL | Robuste, gratuit, capable de gérer relations complexes |
| **Frontend Web** | React + TypeScript | Modern, composable, bon écosystème |
| **Frontend Mobile** | React Native ou Web Responsive | MVP peut être juste web responsive (une cible) |
| **Authentification** | JWT + OAuth | Standard, scalable |
| **Stockage fichiers** | AWS S3 (gratuit tier) ou Cloudinary | Photos, documents |
| **Hosting Backend** | Render / Railway (gratuit tier) | Facile, gratuit au départ |
| **Hosting Frontend** | Vercel / Netlify | Gratuit, déploiement facile |
| **QR Code** | Bibliothèque standard (qr-code JS) | Pas de dépendance externe payante |

---

## 5. SCHÉMA DE BASE DE DONNÉES (Initial)

### 5.1 Entités Principales

```
USERS
├── id (PK)
├── email
├── password_hash
├── nom
├── role (locataire, artisan, admin, promoteur)
├── phone
└── created_at

LOGEMENTS
├── id (PK)
├── adresse
├── numero_logement
├── promoteur_id (FK → USERS)
├── qr_code_token (unique)
├── created_at

DEMANDES (Tickets/Work Orders)
├── id (PK)
├── numero_demande (auto-generated)
├── logement_id (FK → LOGEMENTS)
├── locataire_id (FK → USERS)
├── type_travail (enum: peinture, plomberie, etc.)
├── description
├── statut (nouveau, attribue, en_cours, termine, rejete)
├── urgence (normal, urgent)
├── created_at
├── updated_at

INTERVENTIONS
├── id (PK)
├── demande_id (FK → DEMANDES)
├── artisan_id (FK → USERS)
├── date_intervention
├── description_intervention
├── photos (array of URLs)
├── signature_locataire (base64 ou URL)
├── signature_artisan (base64 ou URL)
├── statut (planifiee, en_cours, terminee)
├── created_at

ATTACHEMENTS (Photos, documents)
├── id (PK)
├── demande_id ou intervention_id (FK)
├── url
├── type (photo, document)
├── created_at

ENTREPRISES
├── id (PK)
├── nom
├── siret
├── contact_principal
├── email
├── phone
├── specialites (array: plomberie, electricite, peinture, etc.)
├── created_at
```

### 5.2 Workflows / States

**Workflow d'une demande :**
```
NOUVEAU → ATTRIBUE → EN_COURS → TERMINE
                ↓
             REJETE (avec raison)
```

---

## 6. ENDPOINTS API (Proposés)

### 6.1 Authentification

```
POST /auth/register
POST /auth/login
POST /auth/logout
POST /auth/refresh-token
```

### 6.2 Demandes (Tickets)

```
POST   /demandes                  (Créer - locataire via QR)
GET    /demandes                  (Lister - avec filtres)
GET    /demandes/{id}             (Détails)
PATCH  /demandes/{id}/statut      (Changer statut)
PATCH  /demandes/{id}/assigner    (Assigner à une entreprise)
DELETE /demandes/{id}             (Soft delete)
```

### 6.3 Interventions

```
POST   /interventions             (Créer - artisan sur place)
GET    /interventions/{id}        (Détails)
PATCH  /interventions/{id}/photos (Ajouter photos)
PATCH  /interventions/{id}/signer (Ajouter signatures)
```

### 6.4 Logements

```
GET    /logements                 (Lister - admin seulement)
POST   /logements                 (Créer - admin seulement)
GET    /logements/{id}            (Détails)
POST   /logements/{id}/qr-code    (Générer/RegénérerQR)
```

### 6.5 Entreprises

```
GET    /entreprises               (Lister)
POST   /entreprises               (Créer - admin)
PATCH  /entreprises/{id}          (Modifier)
```

---

## 7. WIREFRAMES / USER FLOWS (À Visueliser)

### 7.1 Flow Locataire

```
QR Code Scan
    ↓
Page logement pré-remplie
    ↓
Sélectionner type problème
    ↓
Ajouter description + photos
    ↓
Confirmation + N° demande
    ↓
Dashboard suivi (optionnel pour MVP)
```

### 7.2 Flow Artisan

```
Reçoit notification / Consulte app
    ↓
QR Code Scan sur place
    ↓
Voir détails intervention
    ↓
Ajouter photos avant
    ↓
Effectuer travaux
    ↓
Ajouter photos après
    ↓
Signature locataire (doigt sur écran)
    ↓
Signature artisan
    ↓
Envoi / Synchronisation
```

### 7.3 Flow Admin/Promoteur

```
Login → Dashboard
    ↓
Vue globale : toutes les demandes
    ↓
Filtrer / Rechercher
    ↓
Cliquer sur une demande
    ↓
Voir détails + historique
    ↓
Assigner à une entreprise
    ↓
Voir intervention + signatures
```

---

## 8. PLANIFICATION / ROADMAP

### 8.1 Phases de Développement

**Phase 1 (Semaines 1-3) : Setup + Core Backend**
- [ ] Setup projet (git, CI/CD, conventions)
- [ ] Mise en place base de données (migrations)
- [ ] API d'authentification
- [ ] API CRUD de base (logements, demandes)

**Phase 2 (Semaines 4-6) : Frontend Admin + Workflows**
- [ ] Interface admin (dashboard, lister demandes, assigner)
- [ ] Logique de statuts/workflows
- [ ] Notifications basiques

**Phase 3 (Semaines 7-9) : QR Code + Frontend Mobile**
- [ ] Génération QR codes
- [ ] Page de création demande via QR
- [ ] Interface artisan (scan, documenter, signer)

**Phase 4 (Semaines 10-12) : Signatures + Polish**
- [ ] Signature digitale
- [ ] Upload photos
- [ ] Tests et bugfixes
- [ ] Déploiement

**Phase 5 (Après) : Itérations avec testeur**
- [ ] Feedback du promoteur testeur
- [ ] Améliorations
- [ ] Scalabilité

---

## 9. DÉPENDANCES / QUESTIONS EN ATTENTE

### À clarifier avec Damien :

- [ ] **Workflow exact** : Qui reçoit la demande en premier ? (promoteur ? syndic ? entreprise directe ?)
- [ ] **Initialisation des logements** : Qui crée les logements dans l'app ? Comment ?
- [ ] **Photos essentielles ?** : Important dès le départ ou nice-to-have ?
- [ ] **Signatures légales** : Signature simple (proof) ou signature conforme (légale) ?
- [ ] **Client testeur** : Y a déjà quelqu'un en vue pour tester après 3 mois ?

### Blockers techniques identifiés :

- Pas de contraintes identifiées
- Stack choisi basé sur gratuit/open-source (OK pour MVP)

---

## 10. CRITÈRES DE SUCCÈS (MVP)

La v1 est un succès si :

- [ ] Un locataire peut créer une demande en < 2 minutes via QR
- [ ] Un artisan peut documenter et signer une intervention
- [ ] Un admin voit toutes les demandes et leur état en temps réel
- [ ] Le système fonctionne avec ~5 utilisateurs simultanés
- [ ] Les données sont persistées et traçables
- [ ] Damien est satisfait du résultat et prêt à montrer à un client

---

## 11. RESSOURCES & DOCUMENTATION

- Repo Git : (À créer)
- Notion/Wiki : (À créer)
- Figma (wireframes) : (À créer)
- Documentation API : (Swagger/OpenAPI - À générer)

---

**Version suivante :** Inclure réponses de Damien + Affiner user stories + Créer wireframes détaillés