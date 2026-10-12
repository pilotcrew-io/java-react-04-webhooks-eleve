# Sécurité pédagogique — paiement entièrement simulé

L’application est un exercice local. **Aucune carte, aucun IBAN, aucune donnée personnelle, aucun argent réel, aucun compte ni identifiant de prestataire de paiement ne sont nécessaires.** Les seuls moyens proposés sont `CARD_SIMULATED` et `BANK_SIMULATED`. Ne créer aucun champ de numéro de carte, de code de sécurité ou d’identité bancaire, même pour saisir une valeur dite de test.

Le serveur et Vite écoutent uniquement sur l’interface locale pendant les exercices. Le simulateur ne contacte aucun domaine bancaire ou PSP. Les preuves ne contiennent que des références fictives, des sorties de test et les décisions de l’élève. Une mention « Démonstration · aucun paiement réel » reste visible sur les écrans.

Le lab 04 utilise une clé HMAC **publique de démonstration**, `pilotcrew-local-demo-key`. Elle sert à observer ce que change un octet altéré. Toute personne qui lit le cours peut produire une signature valide : cette clé n’authentifie donc aucune organisation réelle. Ce protocole n’est celui d’aucun PSP et ne doit pas être présenté comme une intégration ou une certification bancaire.

Ne pas ajouter de secret réel pour rendre la démonstration « plus réaliste ». Ne pas committer de fichier `.env` contenant des valeurs privées, de jeton, de certificat privé ou de journal personnel. Les variables Vite intégrées au navigateur sont visibles dans l’application : elles ne constituent pas un coffre à secrets. Un incident impliquant une vraie donnée est signalé à l’encadrant avec un minimum de détails, sans publier cette donnée dans une issue.

Les résultats pédagogiques ne prouvent ni conformité réglementaire, ni disponibilité, ni résistance à la fraude, ni capacité à prendre des paiements réels. Une application commerciale demanderait une architecture, des contrôles et une validation adaptés à son contexte. Pour la séance, arrêter uniquement les processus que vous avez lancés et supprimer les sorties locales inutiles selon les consignes de la classe.
