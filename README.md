# plantuml
création de diagramme PlantUML

## Phase 1 : L'Analyse des Besoins (Comprendre le "Quoi")
Commence par définir le périmètre de ton application et ce qu'elle doit faire.

### 1. Diagramme de contexte

But : Définir les frontières de ton système en montrant les acteurs (utilisateurs) et les autres systèmes avec lesquels il communique directement.

Conseil : Ton application doit être représentée comme une simple "boîte noire" au centre. Identifie bien les systèmes externes, par exemple si ton application doit se brancher sur une base de données ERP préexistante pour en extraire des informations.

### 2. Diagramme de cas d'utilisation

But : Lister les grandes fonctionnalités offertes par le système du point de vue de l'utilisateur.

Conseil : Reste purement fonctionnel et évite la technique à ce stade. Utilise des verbes à l'infinitif pour tes cas d'utilisation (ex : "Consulter un tableau de bord", "Générer des statistiques").

### 3. Diagramme d'activités

But : Détailler le flux d'exécution d'un cas d'utilisation ou modéliser un processus métier de bout en bout.

Conseil : Pense-le comme un logigramme amélioré. C'est le moment idéal pour poser sur papier une logique métier avec des conditions complexes (Si / Sinon) ou des actions exécutées en parallèle.

## Phase 2 : La Conception Logique (Comprendre le "Comment")
Maintenant que tu sais ce que fait l'application, tu dois modéliser comment elle est construite en interne.

### 4. Diagramme de classes

But : C'est la colonne vertébrale de ta conception. Il représente la structure statique de ton code (les entités, leurs attributs, leurs méthodes et les relations entre elles).

Conseil : Les jurys du titre CDA scrutent ce diagramme à la loupe. Prends le temps de vérifier rigoureusement toutes tes cardinalités (1..1, 0..*), car il servira souvent de base pour construire ta future base de données relationnelle.

### 5. Diagramme d'objets

But : Prendre une "photographie" de ton diagramme de classes à un instant précis avec des données réelles instanciées.

Conseil : Réalise-le en parallèle ou juste après le diagramme de classes. Utilise-le comme un crash-test pour vérifier si ta structure de classes permet bien de modéliser un scénario réel de ton projet.

### 6. Diagramme de séquence

But : Montrer la chronologie des échanges de messages entre les acteurs, les interfaces et la base de données pour réaliser une action précise.

Conseil : Ne fais surtout pas un diagramme de séquence pour chaque cas d'utilisation. Choisis uniquement le scénario le plus complexe de ton projet pour prouver au jury ta maîtrise de la logique temporelle des objets.

## Phase 3 : L'Architecture (Structure physique et logicielle)
Termine par la vue d'ensemble technique de ta solution.

### 7. Diagramme de composants

But : Montrer l'organisation de ton code en modules logiciels indépendants et leurs dépendances.

Conseil : Ce diagramme est indispensable si tu utilises une architecture scindée, par exemple un framework front-end (comme Vue.js) qui communique via une API avec un framework back-end (comme Laravel).

### 8. Diagramme physique (déploiement ou infrastructure)

But : Représenter la disposition physique du matériel (serveurs, terminaux clients) et l'endroit où les composants logiciels y sont installés.

Conseil : N'oublie pas d'indiquer les protocoles de communication entre les différents nœuds de ton infrastructure (comme le HTTPS pour la navigation web ou le port spécifique pour les requêtes SQL vers ta base de données).

---

## Journal des mises à jour des diagrammes

Ce journal trace les écarts entre la conception initiale (mémoire, chapitres 1-2) et le code réel, pour garder la cohérence documentation ↔ code demandée au jury.

### 22/10/2026 — Règles métier validées avec la Fondation (« Données app EPI.xlsx »)

**Ce qui a changé côté projet** :
- Le code 8.1 / 8.2 / 9.1 est un **tarif** (nature du service pour l'État), partagé par plusieurs types de suivi ; c'est lui qui porte la valeur du forfait (60 / 60 / 30 min, autres = 60).
- La quantité de forfaits saisie par le salarié peut être **décimale**.
- Le ratio suit une **cascade** : total DSK → dispo EPI (− congés − maladie) → dispo client (− formation − supervision − activité interne) ; efficacité réelle = facturables / dispo client, objectif 0,75.

**Diagrammes modifiés** :
| Diagramme | Modification |
|---|---|
| Classes | Nouvelle classe `Tarif` ; `RegleConversion` rattachée au tarif (plus à `CategorieSuivi`) ; `CategorieSuivi` sans `code` unique, avec `tarif_id` ; `DonneeEpilogg.quantite` décimal + `type_prestation` ; `StatistiqueMensuelle` avec les indicateurs de la cascade ; `CategorieHeure` + `Supervision` |

**À reporter dans le mémoire** : les trois règles ci-dessus, et l'exemple chiffré de référence (exemple 1 du fichier, avril 2026 : 128 h → 106 h → 98 h ; 82 h facturables ; efficacité 0,8367). L'exemple « 127 h » du même fichier est incohérent (30 h de congés listées, 2 h déduites) et ne doit pas être repris : point à confirmer avec la Fondation (voir `dev/docs/PLAN-DE-TESTS.md`, § anomalies du 22/10).

### 22/10/2026 (soir) — Alignement sur le moteur ETL codé et le schéma réel d'EpiLogg

| Diagramme | Modification |
|---|---|
| Activité (global) | Partition 2 : cascade réelle (total → dispo EPI → dispo client, efficacité réelle et brute) à la place de « prestations / heures effectives » |
| Séquence 3 | Requête EpiLogg réelle (`benefits` → `attends` → `attend_types`, `interventions`, `service_package_used`) ; stratégie « fenêtre remplacée » en transaction (DSK et EpiLogg) ; consolidation par `ConsolidationService` / `CalculEfficacite` |

**À reporter dans le mémoire** : la découverte du schéma réel (chaîne par `attends`, quantité décimale confirmée, forfait déplacement) et la stratégie de fenêtre remplacée.

### 23/10/2026 (soir) — Interface trilingue (FR / EN / DE) et front étape B (service, globale, administration)

| Diagramme | Modification |
|---|---|
| Composants | Paquet Frontend : composant « Internationalisation (vue-i18n, dictionnaires fr/en/de) » ; couche API : middleware « Langue (Accept-Language) » ; fichiers de langue dans la couche API |
| Contexte | Note : interface et messages d'API disponibles en français, anglais et allemand (Fondation luxembourgeoise) |

**Nouvelle exigence** (William, 23/10/2026) : application disponible en français, anglais et allemand. Implémentée des deux côtés (API : `SetLocale` + `lang/*/epi.php` + `laravel-lang` ; front : `vue-i18n`). **À reporter dans le mémoire** : exigence non fonctionnelle « multilingue » (§ 1.3), choix du français comme langue de référence, mécanisme `Accept-Language`.

Les vues coordinateur / globale / administration consomment les endpoints déjà modélisés (séquences 1 et 2, cas d'utilisation) : aucun nouveau flux, pas de modification des séquences.

### 23/10/2026 (après-midi) — Front Vue.js, étape A (socle, connexion, tableau de bord salarié)

| Diagramme | Modification |
|---|---|
| Composants | Paquet Frontend détaillé : vues et composants (Vue 3, Tailwind, Chart.js), routeur avec gardes par rôle, état global Pinia (session, thème), client HTTP axios (jeton Sanctum, 401 → déconnexion) |
| Déploiement | La SPA est servie par Laravel (coquille HTML + assets compilés par Vite dans `public/build`) ; worker de file d'attente ajouté dans le conteneur applicatif ; EpiLogg = MySQL 8 ; note de déploiement (`npm ci && npm run build`, CRON et worker supervisés) ; DSK = SRV-DATA injoignable via le VPN actuel |

**Choix validés avec William le 23/10** : organisation des écrans et page de connexion inspirées de DSKnet (familières aux salariés), couleurs de la charte EPI (orange `#F39200`, anthracite `#575756`), **menu latéral coloré selon le rôle** (salarié orange, coordinateur ocre, administrateur anthracite), thème clair + sombre automatique, Tailwind + composants maison, livraison en deux étapes (A : socle + tableau de bord salarié ; B : vues coordinateur / globale / administration).

**À reporter dans le mémoire** : la SPA n'est pas un projet séparé mais compilée par Vite et servie par Laravel (un seul déploiement, même origine → pas de CORS) ; le jeton est conservé dans le navigateur (`localStorage`), choix discuté dans PREPARATION-JURY (entrée 11).

### 23/10/2026 — Endpoints d'administration codés (supervision, import manuel, recalcul, qualification, matrice, organigramme)

| Diagramme | Modification |
|---|---|
| Cas d'utilisation | « Qualifier les compteurs DSK » devient « Qualifier les référentiels importés » (compteurs DSK **et** types de suivi EpiLogg : le tarif déduit du `rate_name` est parfois absent, l'Administrateur le fixe) ; « Recalculer » précisé (à la demande ou inclus, en file d'attente) et relié directement à l'Administrateur ; organigramme : activité et mot de passe initial ajoutés |
| Composants | `ReferentielService` dans la couche métier ; composant « File d'attente (jobs) + worker » : les traitements longs (import manuel, recalcul) sont mis en file par les contrôleurs (réponse 202) et exécutés par un worker, qui appelle `EtlService` / `ConsolidationService` |

**Écart signalé** : le mémoire ne mentionnait pas de file d'attente (l'import manuel y est décrit comme un appel direct du contrôleur à l'EtlService). Le code met l'import en file d'attente pour ne pas bloquer une requête HTTP plusieurs minutes (VPN, dizaines de milliers de lignes) ; conséquence pour le déploiement (§ 4) : un worker `php artisan queue:work` doit tourner à côté du CRON. À reporter dans le § 3 (import manuel) et le § 4 (déploiement VPS).

### 22/10/2026 (nuit) — API de consultation codée (lecture du cache, policies de périmètre)

| Diagramme | Modification |
|---|---|
| Séquence 1 | Participant `StatistiquesService` (couche métier) entre le contrôleur et la base ; les trois contrôles dans l'ordre réel (jeton 401, validation 422, policy 403) ; requête réelle (`annee` seule, tri par mois, paramètres liés) ; synthèse annuelle = cascade sur les sommes ; forme du JSON ; note sur la mesure de performance |
| Activité 2 | Année courante par défaut (12 mois + synthèse) au lieu du mois courant ; étape « validation puis policy » ; agrégation par la même cascade |
| Composants | `StatistiquesService`, `ConsolidationService + CalculEfficacite` ajoutés dans la couche métier ; les contrôleurs ne lisent plus l'ORM directement ; flux ETL → consolidation → cache et matrice → recalcul ; base EpiLogg = MySQL 8 (relevé réel) |

**Écart signalé** : le mémoire (§ 2.4) décrivait la lecture du cache depuis le contrôleur ; le code place un service de consultation entre les deux pour porter l'agrégation (rapport des sommes, jamais moyenne des ratios). À reporter dans le § 3 et la diapositive « Architecture en couches ».

### 05/09/2026 — Alignement sur le schéma codé (16/08) et sur les modes d'accès aux sources (mails CBC / Fondation, 26-28/08)

**Ce qui a changé côté projet** :
- EpiLogg = **accès direct à la base en lecture seule via VPN** (plus d'hésitation API / BDD / fichier).
- Aucun identifiant commun DSK ↔ EpiLogg : la correspondance des salariés se fait par **email** (et non par matricule).
- EpiLogg **supprime physiquement** les prestations annulées → l'ETL doit réconcilier, pas seulement incrémenter.
- Le schéma codé a introduit `CompteDsk` (format long des données DSK), `Import` (journal), les énumérations, et des attributs absents du diagramme initial.

**Diagrammes modifiés** :
| Diagramme | Modification |
|---|---|
| Contexte | DSK = API REST/HTTPS, EpiLogg = base SQL lecture seule via VPN ; acteur Planificateur ajouté ; note « correspondance par email » |
| Cas d'utilisation | Ajout : S'authentifier, Qualifier les compteurs DSK, Recalculer, Superviser les imports ; acteur Planificateur ; relations include |
| Activité (global) | Rapprochement par email, réconciliation des suppressions, ratio NULL si 0 minute effective, minutes (pas heures) |
| Activité 1 (import) | Fichiers Excel → connecteurs API / SQL ; deux branches parallèles détaillées ; gestion d'échec |
| Activité 2 (dashboard) | Le système **lit le cache**, il ne recalcule pas ; périmètre selon le rôle |
| Activité 3 (barème) | Transaction, contrôle de chevauchement, `created_by`, recalcul déclenché |
| Classes | Réécrit à partir des migrations/modèles : 9 classes + 4 énumérations, attributs réels (`minutes`, `epilogg_user_id`, `reference_externe`…), scopes, notes de conception |
| Séquence 1 | `user_id`/`annee`, contrôle de rôle, note « lecture du cache uniquement » |
| Séquence 2 | Service métier `MatriceConversionService`, transaction, `created_by` |
| Séquence 3 | Réécrit : Scheduler ou Admin → EtlService → ConnecteurDsk (HTTP) / ConnecteurEpilogg (SQL) → réconciliation → consolidation |
| Composants (`.puml`) | Options EpiLogg remplacées par la BDD ; interface `ConnecteurSource` (Adaptateur) ; trois couches explicites ; `MatriceConversionService` |
| Déploiement (`.puml`) | VPS Ubuntu, MariaDB 12.3, client VPN vers le réseau Fondation (EpiLogg), HTTPS vers DSK ; note sur la contrainte VPN en production |

**À reporter dans le mémoire** : `DonneeDsk` en format long + `CompteDsk` ; table `imports` ; jointure par email ; réconciliation des suppressions EpiLogg ; contrainte VPN pour le déploiement.

Le fichier `test.plum` était un brouillon du diagramme de composants, désormais intégré dans `diagrammes_de_composants.puml` : il peut être supprimé.

### 08/09/2026 — Cas d'utilisation : l'authentification devient une pré-condition globale

**Ce qui a changé** : les quatre relations `<<include>>` qui reliaient « Consulter son tableau de bord », « Consulter les statistiques de son service », « Consulter les statistiques globales » et « Gérer la matrice de conversion » au cas « S'authentifier (token) » sont supprimées. Le cas « S'authentifier » est conservé et relié directement au Salarié (liaison héritée par le Coordinateur et l'Administrateur) ; les `<<extend>>` vers l'export Excel et les trois `<<include>>` vers « Recalculer les statistiques mensuelles » aussi. Une note attachée au rectangle du système énonce la pré-condition globale.

**Pourquoi** :
- Lisibilité : un `include` vers l'authentification depuis chaque cas d'utilisation surcharge le diagramme sans apporter d'information métier — l'authentification est transverse, elle est portée techniquement par un middleware appliqué à toutes les routes de l'API.
- Exactitude : le `include` aurait été faux pour le Planificateur, qui déclenche « Importer les données » depuis le conteneur applicatif sans authentification HTTP. Une pré-condition « pour tout acteur humain » exprime correctement cette exception.
- Les `include` restants gardent un sens métier fort : toute modification des données de calcul (import, barème, qualification d'un compte) déclenche un recalcul.

**À reporter** : § 2.2.2 du dossier de projet (texte et colonne « Préconditions » du tableau des cas d'utilisation), diapositive « Conception fonctionnelle » du support.

### 09/09/2026 — Import : parallélisme limité aux extractions (activité 1, séquence 3)

**Incohérence détectée** : le diagramme d'activité 1 plaçait l'appariement des salariés par e-mail à l'intérieur de la branche EpiLogg, exécutée en parallèle de la branche DSK qui met à jour la table `users`. Or l'appariement dépend des salariés insérés par la branche DSK (dépendance de données entre branches) : selon l'ordre d'exécution, un salarié nouvellement présent dans DSK et EpiLogg pouvait être signalé à tort « sans correspondance ». La séquence 3 était, elle, entièrement séquentielle, et le diagramme global rapprochait les salariés après la jointure : trois représentations différentes du même processus.

**Correction** :
- Activité 1 : le `fork` ne contient plus que les deux extractions (lectures DSK et EpiLogg). Le test d'échec d'extraction est placé juste après la jointure, avant tout enregistrement. Puis, séquentiellement : mise à jour des référentiels (services, compteurs DSK, forfaits normalisés) → upsert des salariés DSK → appariement par e-mail (mémorisation de `epilogg_user_id`, journalisation et exclusion des salariés sans correspondance) → enregistrement idempotent de `donnees_dsk` et `donnees_epilogg` → réconciliation → recalcul → journal « succès ». Note ajoutée.
- Séquence 3 : les deux extractions sont entourées d'un fragment `par` ; les `UPSERT` et l'appariement sont déplacés après le fragment, dans le même ordre ; un `alt` matérialise l'échec d'extraction. Réconciliation, consolidation et clôture du journal inchangées.
- Diagramme global (figure 3) : déjà cohérent (extractions en parallèle, puis rapprochement, enregistrement, réconciliation) ; non modifié.

**À reporter** : § 2.2.3 et § 2.4.3 du dossier de projet, § 3.5 (étapes de l'`EtlService`), diapositive « Le moteur ETL » du support.

