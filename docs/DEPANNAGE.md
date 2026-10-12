# Dépannage — jr-04-webhooks

Relever la première erreur, la commande, la version et le contexte. Une correction conserve le comportement demandé et ajoute ou rejoue une preuve ; retirer une assertion ne répare pas le problème.

| Symptôme | Cause plausible | Diagnostic discriminant | Résolution attendue |
|---|---|---|---|
| Toutes les signatures sont invalides | Les deux côtés ne signent pas les mêmes octets | Comparer longueur du corps et représentation du message de test, sans exposer de vraie donnée | Signer timestamp + point + le même tableau d’octets envoyé ; ne pas re-sérialiser. |
| Un body avec un espace ajouté reste accepté sans nouvelle signature | Le HMAC est calculé sur un objet reconstitué | Altérer le corps après signature dans un test dédié | Vérifier le corps brut reçu avant la conversion JSON. |
| Un test de fraîcheur dépend du jour d’exécution | Le service lit directement l’heure système | Lire la Clock utilisée par le test et par le service | Injecter une Clock fixe et contrôler les bornes 300/301 secondes. |
| Un doublon change encore le statut | Le registre de déduplication ne participe pas à la transition atomique | Envoyer le même eventId deux fois et observer l’état avant/après | Comparer la charge logique et rendre l’opération indivisible. |
| Un terminal SUCCEEDED devient FAILED | Le dernier événement écrase sans règle | Envoyer deux eventIds opposés après une création | Appliquer la table de transitions et renvoyer IGNORED après terminal. |
| Le navigateur continue à requêter après succès | Le timer ne lit pas le nouvel état ou n’est pas nettoyé | Observer les appels GET après l’état terminal | Arrêter la planification au terminal et annuler à la sortie du composant. |
| L’écran annonce un échec après 30 secondes | La borne de polling est confondue avec un événement FAILED | Atteindre 15 lectures toujours PENDING | Afficher attente prolongée et une action explicite de nouvelle lecture. |

Si un port est occupé, identifier le processus avant de l’arrêter. Ne jamais arrêter globalement toutes les JVM ou tous les processus Node d’un poste partagé. Si une commande dépend d’un fichier demandé dans l’atelier, le construire d’abord : les dépôts élèves sont des supports, pas des solutions prêtes à lancer.
