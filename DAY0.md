# Jour 0 — Préparer le cursus Java / React

**PilotCrew · 6 ateliers de 4 h 30 · 27 heures de pratique encadrée.** Préparation hors temps de séance. Version documentaire du 12 octobre 2026.

## À qui s’adresse ce parcours ?

Aux étudiants Bac+4/5 ou développeurs en consolidation qui connaissent déjà les bases Java, JavaScript, HTML, JSON et Git. Le parcours développe la capacité à construire, tester et expliquer une petite tranche fullstack. Il ne promet pas l’expérience d’un poste demandant cinq à dix années de pratique.

L’annonce [Développeur Fullstack Java/React — Fintech](https://consultant.jean-michel.io/annonces/31738) motive les thèmes : Java et React, interfaces de paiement, intégration, rigueur et collaboration Agile. **Java 21, Spring Boot 4.1.1, TypeScript, Vite et le mock PSP sont des choix pédagogiques de PilotCrew**, pas une liste de technologies attribuée à cette annonce.

## Installer et vérifier le poste

Prévoir JDK 21, Maven 3.9.x, Git, curl, Node 22.x au moins 22.12, npm, un navigateur et un terminal Bash (Linux, macOS ou WSL2). Python 3 est utile pour l’émetteur local du lab 04 ; un outil Java équivalent reste possible. Les étapes qui concernent des versions doivent être vérifiées par l’encadrant avant chaque promotion.

```bash
java -version
javac -version
mvn -version
node --version
npm --version
python3 --version
git --version
curl --version
```

Maven utilise le même JDK que le compilateur. Le POM de référence cible Spring Boot 4.1.1 avec les starters `spring-boot-starter-webmvc`, `spring-boot-starter-validation` et `spring-boot-starter-webmvc-test` pour les tests. Utiliser les imports et bibliothèques de tests correspondant à cette version ; ne recopier pas sans vérification une configuration Spring Boot 3. Le parent du projet gère la version de JUnit.

La documentation officielle de [Spring Boot](https://docs.spring.io/spring-boot/system-requirements.html) confirme la compatibilité de Java 21 pour la version retenue. La documentation de [Vite](https://vite.dev/guide/) précise son prérequis Node. Les versions exactes React, TypeScript, Vite et outils de tests sont fixées dans le projet de référence et le lockfile retenus par l’encadrant ; le cours n’utilise pas une mise à jour « latest » automatique pendant une séance.

Précharger les dépendances Maven/npm avec un petit projet jetable ou le contrôle technique de l’encadrant, sans lire les solutions des exercices. Installer le navigateur Playwright avant le lab 05 et vérifier son lancement sur le poste. Prévoir une plage d’accompagnement pour les installations. Une panne d’outil initiale ne doit pas consommer les 270 minutes d’apprentissage.

## Travailler avec six copies séparées

Chaque atelier possède son dépôt élève et son corrigé privé distinct. Les élèves ne reçoivent que le dépôt élève : consignes, mémos et espaces de travail. Les liens présents dans la plateforme doivent correspondre à des dépôts réellement publiés. Tant qu’un lien n’est pas disponible, utiliser l’archive fournie et garder une revue locale.

Avant le lab 01, partir d’une copie neuve puis créer `backend/` pendant la séance. Avant chaque lab suivant :

1. Créer ou cloner la copie personnelle du **nouvel atelier** et vérifier son origine avec `git remote -v`.
2. Copier depuis sa réalisation précédente les dossiers `backend/`, `frontend/` s’il existe, `scripts/` s’il existe, ainsi que les fichiers de configuration et lockfiles associés utiles. Ne pas écraser les nouvelles consignes et les espaces de réponse du nouvel atelier.
3. Exclure `.git/`, `target/`, `node_modules/`, `dist/`, caches, rapports temporaires et données privées. Ne pas recopier un corrigé à la place de son travail.
4. Restaurer les dépendances avec le POM et `npm ci` lorsqu’un lockfile existe. Rejouer les contrôles disponibles au stade atteint.
5. Créer un commit de base dans le nouveau dépôt et noter le dépôt / commit de provenance dans le journal. Cet import est préparé **avant les 4 h 30** du nouveau lab.

| Atelier à préparer | Acquis à reprendre avant la séance |
|---|---|
| 01 · API | Aucun code ; poste Java prêt |
| 02 · React | API et tests Java du 01 |
| 03 · Checkout | API, frontend et tests du 02 |
| 04 · Webhooks | Commande, tentative et idempotence du 03 |
| 05 · Testing | Application et émetteur local du 04 |
| 06 · Product | Application, suites et automatisation du 05 |

Si un acquis manque, l’encadrant donne une base de reprise adaptée et l’élève l’identifie dans son journal. L’évaluation distingue ce qui a été fourni et ce qui a été réalisé pendant le lab. Une base de reprise n’est pas une excuse pour retirer les objectifs de la séance.

## Vérifier le périmètre de démonstration

Aucune carte, aucun IBAN, aucun argent réel, aucune donnée personnelle, aucun compte ou identifiant PSP. Les deux méthodes de paiement sont des noms de simulation. Les serveurs écoutent localement sur 127.0.0.1 ; aucun service tiers de paiement n’est appelé. Le lab de webhook utilise une clé publique de démonstration et un protocole inventé pour les exercices.

Le navigateur ne décide pas le montant ni le statut. Le serveur conserve tout en mémoire, donc perd les références au redémarrage. Cette limite fait partie des exercices. L’interface affiche toujours « Démonstration · aucun paiement réel ».

## Préparer les preuves

Créer un dossier local `preuves/`, ouvrir deux terminaux et préparer le journal du lab. Une preuve associe commande exacte, résultat observé, version et fichier. Noter explicitement les tests non exécutés. Les captures montrent une interface ; les rapports montrent des tests ; les deux ne sont pas interchangeables.

Le formateur confirme avant le départ : outils prêts, dépendances résolues, ports disponibles, dépôt du bon atelier, acquis repris et aucun accès financier nécessaire.
