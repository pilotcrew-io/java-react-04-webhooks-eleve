# Travailler et remettre son atelier

## Démarrer dans une copie identifiable

Utiliser l’archive remise par le formateur ou le lien de dépôt **réellement affiché** dans PilotCrew. Le dépôt élève contient les consignes, mémos et espaces de réponse. Le code demandé est à construire dans `backend/` et `frontend/`. Le corrigé privé ne fait pas partie de cette copie.

Avec une archive, créer un dépôt Git local si nécessaire, puis une branche `atelier/realisation`. Avec un dépôt publié, créer son fork, copier son URL depuis l’interface puis cloner cette URL ; vérifier `git remote -v`. Une éventuelle pull request vise le dépôt personnel de l’élève et sa branche principale. Sans dépôt distant, une revue du diff local reste valable ; écrire « revue locale » au lieu d’inventer une URL.

## Développer une modification à la fois

Lire le contrat, choisir un cas observable, écrire ou compléter son implémentation et sa vérification. Garder des commits compréhensibles. Avant une remise, examiner `git status --short` et `git diff`, vérifier que les fichiers attendus sont suivis et exclure `target/`, `node_modules/`, `dist/`, les rapports volumineux et les secrets. Les rapports utiles peuvent être remis à part dans un dossier de preuves.

Les six labs composent un même fil rouge mais utilisent **six dépôts élèves distincts**. Avant chaque nouvelle séance, créer son nouveau fork ou sa copie du nouvel atelier, puis y reprendre les fichiers d’application de sa réalisation précédente. Exclure .git, target, node_modules, dist, caches et secrets, conserver les nouvelles consignes et noter le commit de provenance. Voir DAY0.md pour la procédure complète. Cet import et les contrôles de départ sont hors des 270 minutes. Si un retard empêche la reprise, l’encadrant fournit une base **signalée comme telle** ; noter ce qui a été fourni et ce qui a été réalisé.

## Remettre des preuves honnêtes

Compléter `travail/REPONSES.md`, le backlog et le modèle de revue. Pour une vérification, indiquer la commande exacte, le résultat observé, la version des outils, le commit et le fichier de preuve. Un test écrit mais non exécuté porte explicitement la mention « non exécuté ». Une capture d’écran ne remplace pas un test de contrat ; un test unitaire ne remplace pas une vérification dans le navigateur.

Faire relire le travail par un pair ou l’encadrant, puis répondre à une question sur le comportement et une question sur une limite. L’oral peut se terminer par une minute en anglais. Aucune publication, ouverture de compte PSP ou dépense n’est nécessaire à la validation de ces ateliers.
