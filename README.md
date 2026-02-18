# 🚖 Rapido — Gestion de Courses Interurbaines

> Application web de gestion de courses pour transport interurbain : réservations, affectation de chauffeurs et suivi des statuts en temps réel.

---

## 📌 Contexte

**Rapido** est une application concrète développée pour répondre à un besoin réel de gestion de courses dans le secteur du transport interurbain. Elle permet à un opérateur de créer des courses, d'affecter des chauffeurs disponibles et de suivre l'état de chaque trajet du début à la fin.

---

## ✨ Fonctionnalités

- 🧾 **Création d'une course** — Point de départ, point d'arrivée, date et heure
- 👨‍✈️ **Affectation d'un chauffeur** — Sélection parmi les chauffeurs disponibles
- 📋 **Tableau de bord des courses** — Vue d'ensemble triée par date (du plus récent)
- 🔄 **Gestion des statuts** — `en_attente` → `en_cours` → `terminée`
- 🔒 **Sécurité des actions** — Vérification du statut avant chaque action (anti-doublon)
- 📊 **Requêtes SQL avancées** — Jointures, vues complètes, statistiques par statut

---

## 🛠 Stack Technique

| Couche | Technologie |
|---|---|
| Langage backend | **PHP natif** (sans framework) |
| Base de données | **PostgreSQL 15** |
| Interface BDD | **pgAdmin** |
| Serveur web | **Apache via XAMPP** |
| Frontend | HTML / CSS / JavaScript |
| Architecture | **MVC** (Model – View – Controller) |

> Aucun framework PHP (pas de Laravel, pas de Symfony). Tout est construit en PHP natif avec PDO pour la connexion à PostgreSQL.

---

## 📁 Structure du Projet

```
rapido/
├── config.php      # Connexion à PostgreSQL (PDO)
├── index.php       # Page principale — tableau des courses + modals
├── actions.php     # Traitement des formulaires (ajouter, affecter, terminer)
├── style.css       # Styles visuels
├── app.js          # Ouverture/fermeture des modals
└── rapido.sql      # Script SQL — création des tables et données initiales
```

---

## 🗄 Modèle de Données

### Table `courses`
| Colonne | Type | Description |
|---|---|---|
| `course_id` | SERIAL PK | Identifiant unique |
| `point_depart` | VARCHAR | Lieu de départ |
| `point_arrivee` | VARCHAR | Lieu d'arrivée |
| `date_heure` | TIMESTAMP | Date et heure de la course |
| `statut` | VARCHAR | `en_attente` / `en_cours` / `terminee` |
| `chauffeur_id` | FK | Chauffeur affecté (nullable) |

### Table `chauffeurs`
| Colonne | Type | Description |
|---|---|---|
| `chauffeur_id` | SERIAL PK | Identifiant unique |
| `nom` | VARCHAR | Nom du chauffeur |
| `prenoms` | VARCHAR | Prénoms |
| `telephone` | VARCHAR | Contact |
| `sexe` | CHAR(1) | M / F |
| `disponible` | BOOLEAN | Disponibilité |

---

## ⚙️ Les 3 Actions Principales

### 1. Ajouter une course
Crée une nouvelle course avec le statut `en_attente`. Utilise des requêtes préparées PDO (`prepare` / `execute`) pour se protéger des injections SQL.

### 2. Affecter un chauffeur
Vérifie d'abord que la course est toujours `en_attente`, puis met à jour le `chauffeur_id` et passe le statut à `en_cours`. La double vérification (côté service + côté SQL avec `AND statut = 'en_attente'`) protège contre les conflits concurrents.

### 3. Terminer une course
Passe la course de `en_cours` à `terminee`. Seules les courses `en_cours` peuvent être terminées.

---

## 🚀 Installation & Démarrage

### Prérequis
- PostgreSQL 15 installé
- pgAdmin (interface de gestion BDD)
- XAMPP installé (Apache + PHP uniquement — **ne pas utiliser MySQL de XAMPP**)

### Étapes

**1. Installer XAMPP et activer l'extension PostgreSQL**

Dans `C:\xampp\php\php.ini`, décommenter :
```ini
extension=pdo_pgsql
extension=pgsql
```
Puis démarrer Apache depuis le XAMPP Control Panel.

**2. Créer la base de données dans pgAdmin**

- Ouvrir pgAdmin → clic droit sur "Databases" → "Create" → nommer `rapido`
- Ouvrir le Query Tool sur la base `rapido`
- Copier-coller le contenu de `rapido.sql` et exécuter (F5)

**3. Placer le projet**

```
Copier le dossier rapido/ dans : C:\xampp\htdocs\
```

**4. Configurer la connexion**

Dans `config.php`, renseigner le mot de passe PostgreSQL :
```php
define('DB_PASS', 'ton_mot_de_passe');
```

**5. Lancer l'application**

Ouvrir le navigateur et accéder à :
```
http://localhost/rapido
```

✅ Le tableau des courses doit s'afficher.

---

## 🔍 Requêtes SQL Utiles

```sql
-- Vue complète courses + chauffeur affecté
SELECT c.course_id, c.point_depart, c.point_arrivee,
       TO_CHAR(c.date_heure, 'DD/MM/YYYY HH24:MI') AS date_heure,
       c.statut,
       COALESCE(ch.prenoms || ' ' || ch.nom, 'Non assigné') AS chauffeur
FROM courses c
LEFT JOIN chauffeurs ch ON c.chauffeur_id = ch.chauffeur_id
ORDER BY c.date_heure DESC;

-- Courses par statut
SELECT statut, COUNT(*) AS nombre FROM courses GROUP BY statut;

-- Ajouter un chauffeur
INSERT INTO chauffeurs (nom, prenoms, telephone, sexe)
VALUES ('BAMBA', 'Seydou', '+229 07 55 44 33', 'M');
```

---

## 🐛 Résolution des problèmes fréquents

| Problème | Cause | Solution |
|---|---|---|
| Page blanche | Apache pas lancé | Démarrer Apache dans XAMPP |
| Erreur connexion PostgreSQL | Mauvais mot de passe | Modifier `DB_PASS` dans `config.php` |
| "driver not found" | Extension PDO non activée | Décommenter `extension=pdo_pgsql` dans `php.ini` |
| Pas de données | Script SQL non exécuté | Exécuter `rapido.sql` dans pgAdmin |

---

## 👨‍💻 Auteur

**Folawè Milarépa AGLI**  
Étudiant en Licence Professionnelle SIL — UATM GASA Formation  
[github.com/aglifolawe-svg](https://github.com/aglifolawe-svg)

---

## 📄 Licence

Projet académique à but pédagogique.  
Libre d'utilisation à des fins d'apprentissage.
