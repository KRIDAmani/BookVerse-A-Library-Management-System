# Projet 2 - Modélisation Entité-Association
## Base de Données de Bibliothèque ECL

---

## 1.1. Conception du modèle de données

### 1.1.1. Diagramme Entité-Association

Le diagramme E-A sera réalisé avec DBMain (voir fichier séparé).

---

## 1.1.2. Définitions textuelles des types d'entité

### PUBLICATION
**Description :** Entité générique représentant toute publication dans la bibliothèque.

**Attributs :**
- `id_publication` (clé primaire) : Identifiant unique de la publication
- `titre` : Titre de la publication
- `annee_publication` : Année de publication
- `type_publication` : Type (LIVRE, PERIODIQUE, RAPPORT_INTERNE)
- `statut_disponibilite` : Statut actuel (ON_RACK, ISSUED_TO, LOST, TO_BE_BOUGHT)

---

### LIVRE (spécialisation de PUBLICATION)
**Description :** Publications de type livre régulier avec ISBN.

**Attributs spécifiques :**
- `ISBN` (clé candidate) : Numéro ISBN unique identifiant le livre
- `editeur` : Maison d'édition
- `edition` : Numéro ou nom de l'édition

---

### PERIODIQUE (spécialisation de PUBLICATION)
**Description :** Publications périodiques (revues, magazines scientifiques).

**Attributs spécifiques :**
- `numero_volume` : Numéro du volume
- `editeur` : Maison d'édition
- `edition` : Édition du périodique

---

### RAPPORT_INTERNE (spécialisation de PUBLICATION)
**Description :** Rapports internes de type ECL, thèses de doctorat ou rapports scientifiques.

**Attributs spécifiques :**
- `categorie_rapport` : Type de rapport (ECL, PHD_THESIS, SCIENTIFIC)

---

### AUTEUR
**Description :** Auteurs des publications.

**Attributs :**
- `id_auteur` (clé primaire) : Identifiant unique de l'auteur
- `nom` : Nom de famille de l'auteur
- `prenom` : Prénom de l'auteur

---

### LABORATOIRE
**Description :** Laboratoires ECL partageant la base de données.

**Attributs :**
- `id_laboratoire` (clé primaire) : Identifiant unique du laboratoire
- `nom_laboratoire` : Nom du laboratoire
- `adresse` : Adresse physique du laboratoire

---

### UTILISATEUR
**Description :** Utilisateurs de la bibliothèque identifiés par leur email.

**Attributs :**
- `email` (clé primaire) : Adresse email de l'utilisateur
- `nom` : Nom de l'utilisateur
- `prenom` : Prénom de l'utilisateur

---

### LIBRAIRIE
**Description :** Magasins où les publications ont été achetées.

**Attributs :**
- `id_librairie` (clé primaire) : Identifiant unique de la librairie
- `nom_librairie` : Nom du magasin
- `adresse` : Adresse de la librairie

---

### MOT_CLE
**Description :** Mots-clés associés aux publications et aux intérêts des utilisateurs.

**Attributs :**
- `id_mot_cle` (clé primaire) : Identifiant unique du mot-clé
- `libelle` : Texte du mot-clé

---

### CATEGORIE
**Description :** Catégories de classification des livres (maximum 4 par livre).

**Attributs :**
- `id_categorie` (clé primaire) : Identifiant unique de la catégorie
- `nom_categorie` : Nom de la catégorie

---

### PROPOSITION_ACHAT
**Description :** Propositions d'achat de nouvelles publications par les utilisateurs.

**Attributs :**
- `id_proposition` (clé primaire) : Identifiant unique de la proposition
- `date_proposition` : Date de la proposition
- `titre_propose` : Titre de la publication proposée
- `type_publication` : Type (LIVRE, PERIODIQUE, RAPPORT_INTERNE)
- `isbn_propose` : ISBN si livre
- `auteurs_proposes` : Informations sur les auteurs

---

### TAUX_CHANGE
**Description :** Taux de change des devises pour la conversion des prix.

**Attributs :**
- `id_taux` (clé primaire) : Identifiant unique
- `devise` : Code de la devise (EUR, USD, GBP)
- `taux_vers_euro` : Taux de conversion vers l'euro
- `date_mise_a_jour` : Date de mise à jour du taux

---

## 1.1.3. Définitions textuelles des types d'association

### ECRIT_PAR
**Description :** Association entre PUBLICATION et AUTEUR.

**Cardinalités :**
- PUBLICATION (1,1) — (1,n) AUTEUR
- Pour un LIVRE : minimum 1, maximum 4 auteurs
- Pour un RAPPORT_INTERNE (thèse) : 1 auteur principal, nombre illimité d'auteurs secondaires

**Attributs :**
- `ordre_auteur` : Position de l'auteur dans la liste (1er, 2e, etc.)
  
---

### POSSEDE
**Description :** Association entre LABORATOIRE et PUBLICATION (possession d'une copie).

**Cardinalités :**
- LABORATOIRE (1,1) — (0,n) PUBLICATION
- PUBLICATION (1,1) — (1,1) LABORATOIRE

**Remarque :** Une seule copie de chaque publication est possédée par un seul laboratoire.

---

### ACHETE_A
**Description :** Association entre PUBLICATION et LIBRAIRIE (lieu et conditions d'achat).

**Cardinalités :**
- PUBLICATION (1,1) — (0,1) LIBRAIRIE
- LIBRAIRIE (1,1) — (0,n) PUBLICATION

**Attributs :**
- `prix_achat` : Prix payé lors de l'achat
- `devise` : Devise utilisée (EUR, USD, GBP)
- `date_achat` : Date de l'achat

---

### EMPRUNTE
**Description :** Association entre UTILISATEUR et PUBLICATION (emprunt en cours).

**Cardinalités :**
- UTILISATEUR (0,1) — (0,n) PUBLICATION
- PUBLICATION (1,1) — (0,1) UTILISATEUR

**Attributs :**
- `date_emprunt` : Date de début de l'emprunt
- `date_retour_prevue` : Date prévue de retour

**Remarque :** Seulement si statut_disponibilite = 'ISSUED_TO'

---

### AUTORISE_A_EMPRUNTER
**Description :** Association entre UTILISATEUR et LABORATOIRE (droits d'accès).

**Cardinalités :**
- UTILISATEUR (0,1) — (1,n) LABORATOIRE
- LABORATOIRE (1,1) — (0,n) UTILISATEUR

**Remarque :** Un utilisateur peut avoir accès aux publications de plusieurs laboratoires.

---

### INTERESSE_PAR (Utilisateur - Mot-clé)
**Description :** Association entre UTILISATEUR et MOT_CLE (centres d'intérêt).

**Cardinalités :**
- UTILISATEUR (0,1) — (0,n) MOT_CLE
- MOT_CLE (1,1) — (0,n) UTILISATEUR

---

### DECRIT_PAR (Publication - Mot-clé)
**Description :** Association entre PUBLICATION et MOT_CLE.

**Cardinalités :**
- PUBLICATION (1,1) — (0,n) MOT_CLE
- MOT_CLE (1,1) — (0,n) PUBLICATION

---

### APPARTIENT_A (Livre - Catégorie)
**Description :** Association entre LIVRE et CATEGORIE.

**Cardinalités :**
- LIVRE (1,1) — (0,4) CATEGORIE
- CATEGORIE (1,1) — (0,n) LIVRE

**Contrainte :** Maximum 4 catégories par livre.

---

### PROPOSE_ACHAT
**Description :** Association entre UTILISATEUR et PROPOSITION_ACHAT.

**Cardinalités :**
- UTILISATEUR (0,1) — (0,n) PROPOSITION_ACHAT
- PROPOSITION_ACHAT (1,1) — (1,1) UTILISATEUR

---

## 1.1.4. Contraintes d'intégrité

### Contraintes d'entité

**CI-1 : Identifiants uniques**
- Chaque PUBLICATION doit avoir un id_publication unique
- Chaque LIVRE doit avoir un ISBN unique
- Chaque UTILISATEUR doit avoir un email unique
- Chaque AUTEUR doit avoir un id_auteur unique
- Chaque LABORATOIRE doit avoir un id_laboratoire unique

**CI-2 : Spécialisation de PUBLICATION**
- Une PUBLICATION doit être exactement d'un type : LIVRE, PERIODIQUE ou RAPPORT_INTERNE
- La spécialisation est totale et disjointe

**CI-3 : Valeurs de statut**
- Le statut_disponibilite doit être dans {'ON_RACK', 'ISSUED_TO', 'LOST', 'TO_BE_BOUGHT'}

**CI-4 : Valeurs de type_publication**
- Le type_publication doit être dans {'LIVRE', 'PERIODIQUE', 'RAPPORT_INTERNE'}

**CI-5 : Catégorie de rapport**
- Pour RAPPORT_INTERNE, categorie_rapport doit être dans {'ECL THESIS', 'PHD_THESIS', 'SCIENTIFIC'}

**CI-6 : Devises acceptées**
- Les devises acceptées sont : 'EUR', 'USD', 'GBP' (Livre Sterling)

---

### Contraintes d'association

**CI-7 : Nombre d'auteurs pour LIVRE**
- Un LIVRE doit avoir entre 1 et 4 auteurs
- `COUNT(AUTEUR par LIVRE) >= 1 AND COUNT(AUTEUR par LIVRE) <= 4`

**CI-8 : Auteur principal pour thèse**
- Un RAPPORT_INTERNE de type PHD_THESIS doit avoir exactement 1 auteur principal
- `COUNT(AUTEUR pour PHD_THESIS) = 1`

**CI-9 : Maximum de catégories par livre**
- Un LIVRE peut avoir au maximum 4 catégories
- `COUNT(CATEGORIE par LIVRE) <= 4`

**CI-10 : Cohérence emprunt-disponibilité**
- Si une PUBLICATION a statut_disponibilite = 'ISSUED_TO', elle doit être associée à exactement un UTILISATEUR via EMPRUNTE
- Si statut_disponibilite ≠ 'ISSUED_TO', elle ne doit pas être associée à un UTILISATEUR via EMPRUNTE

**CI-11 : Droits d'emprunt**
- Un UTILISATEUR peut emprunter une PUBLICATION d'un LABORATOIRE seulement s'il est autorisé à emprunter dans ce laboratoire
- `∀ emprunt : UTILISATEUR.autorisé_dans(PUBLICATION.LABORATOIRE) = TRUE`

**CI-12 : Propriété unique**
- Une PUBLICATION ne peut appartenir qu'à un seul LABORATOIRE
- `COUNT(LABORATOIRE par PUBLICATION) = 1`

**CI-13 : Achat unique**
- Une PUBLICATION ne peut avoir été achetée qu'à une seule LIBRAIRIE
- `COUNT(LIBRAIRIE par PUBLICATION) <= 1`

**CI-14 : Publications "à acheter"**
- Si statut_disponibilite = 'TO_BE_BOUGHT', la publication ne doit pas avoir d'association avec LIBRAIRIE (pas encore achetée)

---

### Contraintes de domaine

**CI-15 : Format ISBN**
- L'ISBN doit respecter le format ISBN-10 ou ISBN-13
- Format : ISBN-10 (10 chiffres) ou ISBN-13 (13 chiffres avec préfixe 978 ou 979)

**CI-16 : Format email**
- L'email doit respecter le format standard : `utilisateur@domaine.extension`

**CI-17 : Années valides**
- L'année de publication doit être >= 1000 et <= année courante + 1

**CI-18 : Prix positifs**
- Le prix_achat doit être > 0

**CI-19 : Taux de change positifs**
- Le taux_vers_euro doit être > 0

**CI-20 : Dates cohérentes pour emprunts**
- date_retour_prevue doit être >= date_emprunt
- date_emprunt doit être <= date courante

---

### Contraintes métier

**CI-21 : Notification d'emprunt disponible**
- Lorsqu'une PUBLICATION devient disponible (statut passe à 'ON_RACK') et qu'elle correspond aux mots-clés d'intérêt d'un UTILISATEUR ayant emprunté une copie précédemment, cet utilisateur doit être notifié par email

**CI-22 : Proposition d'achat complète**
- Une PROPOSITION_ACHAT pour un LIVRE doit inclure au minimum : titre, ISBN, et au moins un auteur

**CI-23 : Unicité des publications**
- Deux LIVRE ne peuvent avoir le même ISBN
- Pour éviter les doublons, vérifier (titre + année + premier_auteur) avant insertion

**CI-24 : Conversion de devises**
- Les prix en devises étrangères (USD, GBP) doivent pouvoir être convertis en EUR en utilisant les taux de change actuels de la table TAUX_CHANGE

---

## 1.1.5. Règles de gestion complémentaires

**RG-1 : Gestion des emprunts**
- Un utilisateur ne peut emprunter une publication que si elle est 'ON_RACK' et qu'il a les droits d'accès au laboratoire propriétaire
- Lors de l'emprunt, le statut passe automatiquement à 'ISSUED_TO'
- Lors du retour, le statut repasse à 'ON_RACK'

**RG-2 : Publications perdues**
- Si une publication est marquée comme 'LOST', elle reste dans le système pour historique mais ne peut plus être empruntée
- Le laboratoire propriétaire peut décider de racheter la publication (nouvelle entrée avec statut 'TO_BE_BOUGHT')

**RG-3 : Notification des utilisateurs intéressés**
- Quand une nouvelle publication est ajoutée, tous les utilisateurs dont les mots-clés correspondent aux mots-clés de la publication reçoivent une notification email

**RG-4 : Traitement des propositions**
- Les propositions d'achat sont examinées par les gestionnaires de laboratoire
- Une fois approuvée, une nouvelle entrée PUBLICATION est créée avec statut 'TO_BE_BOUGHT'

**RG-5 : Conversion et affichage des prix**
- Les prix sont stockés dans leur devise d'origine
- Pour les requêtes d'évaluation (Query 3), les prix sont convertis en EUR en utilisant les taux actuels
- Les taux de change doivent être mis à jour régulièrement

---

## 1.1.6. Schéma conceptuel résumé

### Entités principales
1. PUBLICATION (entité mère)
   - LIVRE (héritage)
   - PERIODIQUE (héritage)
   - RAPPORT_INTERNE (héritage)

2. AUTEUR
3. LABORATOIRE
4. UTILISATEUR
5. LIBRAIRIE
6. MOT_CLE
7. CATEGORIE
8. PROPOSITION_ACHAT
9. TAUX_CHANGE

### Relations principales
- ECRIT_PAR (PUBLICATION ↔ AUTEUR)
- POSSEDE (LABORATOIRE ↔ PUBLICATION)
- ACHETE_A (PUBLICATION ↔ LIBRAIRIE)
- EMPRUNTE (UTILISATEUR ↔ PUBLICATION)
- AUTORISE_A_EMPRUNTER (UTILISATEUR ↔ LABORATOIRE)
- INTERESSE_PAR (UTILISATEUR ↔ MOT_CLE)
- DECRIT_PAR (PUBLICATION ↔ MOT_CLE)
- APPARTIENT_A (LIVRE ↔ CATEGORIE)
- PROPOSE_ACHAT (UTILISATEUR ↔ PROPOSITION_ACHAT)

---

## 1.1.7. Notes de conception

**Choix de modélisation :**

1. **Héritage pour les types de publications** : Utilisation d'une spécialisation totale et disjointe pour LIVRE, PERIODIQUE et RAPPORT_INTERNE, permettant d'ajouter des attributs spécifiques à chaque type tout en partageant les attributs communs.

2. **Gestion des devises** : Stockage des prix dans leur devise d'origine avec une table de taux de change pour permettre les conversions, assurant la traçabilité historique des coûts réels.

3. **Mots-clés normalisés** : Table séparée pour les mots-clés permettant la réutilisation et facilitant les recherches et notifications.

4. **Statut de disponibilité** : Attribut énuméré dans PUBLICATION plutôt qu'une table séparée pour simplifier les requêtes fréquentes.

5. **Proposition d'achat séparée** : Entité distincte car les propositions ne sont pas encore des publications réelles et ont un cycle de vie différent.

---

**Document créé dans le cadre du Projet 2 - Base de Données de Bibliothèque ECL**
