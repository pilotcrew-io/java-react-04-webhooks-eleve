# Évaluation — jr-04-webhooks · 20 points

| Critère | Points | Preuve attendue |
|---|---:|---|
| HMAC brut, format et fenêtre d’horloge | 4 | Signature valide, altération, absence et tests des bornes à Clock fixe. |
| Cohérence métier de l’événement | 3 | Rejet du montant faux signé, de la devise et de la référence inconnue. |
| Déduplication et transition atomiques | 4 | Doublon, concurrence, eventId réutilisé et une seule transition. |
| Événements tardifs sans régression | 3 | Table et test opposé après terminal → IGNORED. |
| Suivi React borné, annulable et compréhensible | 4 | Tests et navigateur : terminal arrête, 15 lectures restent attente, nettoyage. |
| Trace, revue et limites de simulation | 2 | Références corrélées et distinction primitive HMAC / authentification réelle. |
| **Total** | **20** | |
## Règles de restitution

Un fichier présent n’obtient pas à lui seul tous les points : montrer son comportement et pouvoir l’expliquer. Les cas négatifs sont aussi importants que le nominal. Le code rendu ne contient aucune vraie donnée bancaire ou identité PSP ; tous les scénarios restent locaux et fictifs.

Associer chaque preuve à la version remise. Le journal mentionne l’acquis repris du lab précédent et toute base fournie par l’encadrant. Une revue locale est recevable lorsqu’aucun dépôt distant n’est utilisé. Ne pas créer de fausse URL de commit, de pull request ou de rapport.

En cas de panne d’outillage documentée, l’encadrant distingue les critères vérifiés sur un poste de secours, les contrôles relus mais non exécutés et les preuves reportées. Un test navigateur non exécuté ne reçoit pas l’étiquette « E2E réussi ». Une reprise de preuve peut être organisée ; le résultat obtenu reste daté et attribué au bon environnement.

Pour valider la séance, le formateur dispose d’un ensemble fonctionnel sur les objectifs obligatoires, de tests pertinents, d’un diagnostic expliqué et de limites honnêtes. Une extension ne compense pas un montant client accepté, une duplication masquée ou un faux succès affiché.

## Questions d’oral

1. Expliquer ce que protège le HMAC et ce que la clé publique ne protège pas.
2. Distinguer montant cohérent et signature valide.
3. Décrire la différence entre IGNORED, DUPLICATE et une erreur 409.
