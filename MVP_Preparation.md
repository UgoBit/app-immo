# Document de Préparation - Projet SAV Immobilier

**Projet :** Application de suivi des travaux de garantie en immobilier neuf  
**Version :** 0.2 (Spécifications confirmées)  
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
- Les Quitus (attestations de fin de travaux) doivent être transmis manuellement à tous les intermédiaires

**Impact métier :**
- Délais allongés pour résoudre les problèmes
- Mauvaise expérience client (locataires/propriétaires frustés)
- Inefficacité administrative pour les promoteurs/syndics
- Perte de documents/signatures critiques

### 1.2 La Solution Proposée

Une **plateforme centralisée** qui :
- Permet au promoteur de créer sa résidence et ses logements
- Génère automatiquement des QR codes associés à chaque logement
- Permet aux occupants (locataires/propriétaires) de signaler les problèmes via QR code
- Automatise le routage des demandes via la chaîne d'intervenants (Promoteur → MOE → Entreprise)
- Permet à l'entreprise de documenter l'intervention (photos avant/après) et faire signer un **Quitus** à l'occupant
- Distribue automatiquement le Quitus à tous les intermédiaires

### 1.3 Cible Utilisateurs (MVP)

| Rôle | Description | Nombre estimé |
|------|-------------|---------------|
| **Propriétaire/Locataire** | Crée les demandes de SAV via QR | Variable |
| **Gestionnaire** | Reçoit/valide les demandes (optionnel selon cas) | Variable |
| **Promoteur** | Crée la résidence, logements, entreprises; gère les demandes | 1-2 (MVP) |
| **Maître d'œuvre (MOE)** | Valide et assigne l'intervention à l'entreprise | 1-2 (MVP) |
| **Artisan/Entreprise** | Effectue les travaux et fait signer le Quitus | 5-10 (MVP) |
| **Admin/Coordinateur** | Gestion globale (peut être Damien) | 1 |

---

## 2. OBJECTIFS DU MVP

### 2.1 Objectifs Primaires

- [ ] Permettre au promoteur de créer une résidence avec ses logements
- [ ] Générer automatiquement des QR codes pour chaque logement
- [ ] Permettre à un occupant de créer une demande via QR code en < 2 min
- [ ] Router automatiquement la demande dans la chaîne : Promoteur → MOE → Entreprise
- [ ] Permettre à l'entreprise de documenter l'intervention avec photos et faire signer le Quitus
- [ ] Distribuer automatiquement le Quitus à tous les intermédiaires
- [ ] Permettre au promoteur de suivre toutes les demandes en temps réel

### 2.2 Objectifs Secondaires (à affiner après tests)

- [ ] Notifications/alertes en temps réel à chaque étape
- [ ] Historique complet de chaque demande
- [ ] Export de rapports (données de suivi, Quitus)

### 2.3 Ce qui N'EST PAS au MVP

- Signature légale conforme (signature digitale simple pour valider)
- Intégration de devis/facturation
- Multi-langue
- App native iOS (web responsive suffit)
- Gestion des gestionnaires (cas propriétaire → gestionnaire) - peut être ajouté après

---

## 3. ANALYSE DES BESOINS

### 3.1 User Stories - PROMOTEUR (ADMIN)

```
En tant que promoteur/administrateur du système,
Je veux créer et gérer mes résidences et logements,
Afin de les rendre disponibles pour les demandes de SAV.

Critères d'acceptation :
- Je peux créer une résidence avec ses informations
- Je peux ajouter des logements à la résidence avec leurs adresses/numéros
- Un QR code unique est généré automatiquement pour chaque logement
- Je peux télécharger/imprimer les QR codes
- Je peux ajouter les entreprises agréées avec leurs coordonnées
- Je vois un dashboard avec toutes mes demandes de SAV
- Je peux filtrer par statut, urgence, type de travaux
- Je peux voir le parcours complet de chaque demande
```

### 3.2 User Stories - OCCUPANT (Locataire/Propriétaire)

```
En tant que locataire ou propriétaire d'un logement neuf,
Je veux pouvoir signaler rapidement un problème,
Afin que le problème soit traité rapidement.

Critères d'acceptation :
- Je scanne un QR code avec mon téléphone
- Je suis dirigé vers un formulaire pré-rempli avec mon adresse/logement
- Je renseigne : type de problème, description, urgence, photos
- Je reçois une confirmation de création avec un numéro de demande
- Je peux voir l'état de ma demande
- Je dois signer le Quitus une fois l'intervention terminée
```

### 3.3 User Stories - MAÎTRE D'ŒUVRE (MOE)

```
En tant que maître d'œuvre,
Je veux recevoir et valider les demandes de SAV,
Afin de les assigner à l'entreprise compétente.

Critères d'acceptation :
- Je reçois une notification pour chaque nouvelle demande
- Je peux voir les détails de la demande
- Je peux assigner la demande à une entreprise
- Je peux voir l'état d'avancement de l'intervention
- Je reçois le Quitus une fois signé par l'occupant
```

### 3.4 User Stories - ARTISAN/ENTREPRISE

```
En tant qu'artisan envoyé pour une intervention,
Je veux confirmer ma présence et documenter l'intervention,
Afin de laisser une trace de ce qui a été fait et valider l'intervention.

Critères d'acceptation :
- Je reçois une notification pour chaque intervention assignée
- Je scanne un QR code sur place pour accéder à la demande
- Je vois les détails de l'intervention à effectuer
- Je peux ajouter des photos avant/après les travaux
- Je peux faire signer un Quitus à l'occupant (signature digitale sur écran)
- Je signe aussi le Quitus pour valider
- Le Quitus est transmis automatiquement à tous les intermédiaires
```

---

## 4. WORKFLOWS DÉTAILLÉS

### 4.1 Workflow Complet d'une Demande

```
1. CRÉATION STRUCTURE
   Promoteur crée Résidence → Logements → QR Codes générés
   Promoteur ajoute Entreprises agréées

2. CRÉATION DE DEMANDE
   Occupant scanne QR → Remplissage formulaire → Création demande (Statut: NOUVEAU)

3. VALIDATION PROMOTEUR
   Promoteur reçoit notif → Valide demande → Assigne au MOE (Statut: ATTRIBUE_MOE)

4. VALIDATION MOE
   MOE reçoit notif → Valide demande → Assigne à Entreprise (Statut: ATTRIBUE_ENTREPRISE)

5. INTERVENTION
   Entreprise reçoit notif → Scanne QR sur place → Ajoute photos avant
   → Effectue travaux → Ajoute photos après (Statut: EN_COURS)

6. SIGNATURE QUITUS
   Entreprise & Occupant signent le Quitus (Statut: QUITUS_SIGNE)

7. DISTRIBUTION QUITUS
   Quitus distribué automatiquement à:
   - Promoteur ✓
   - MOE ✓
   - Occupant ✓
   Statut final: TERMINE
```

### 4.2 États / Statuts

```
NOUVEAU → ATTRIBUE_MOE → ATTRIBUE_ENTREPRISE → EN_COURS → QUITUS_SIGNE → TERMINE

Possible aussi :
→ REJETE (si pas conforme ou autre raison)
```

### 4.3 Rôles & Permissions

| Rôle | Créer demande | Valider | Assigner | Voir tout | Signer Quitus |
|------|---------------|---------|----------|-----------|---------------|
| **Occupant** | ✓ | ✗ | ✗ | ✗ (juste sienne) | ✓ |
| **Promoteur** | ✗ | ✓ | ✓ (au MOE) | ✓ | ✗ |
| **MOE** | ✗ | ✓ | ✓ (à Entreprise) | ✓ (filtrées) | ✗ |
| **Entreprise** | ✗ | ✗ | ✗ | ✗ (assignées) | ✓ |
| **Admin** | ✓ | ✓ | ✓ | ✓ | ✓ |

---
