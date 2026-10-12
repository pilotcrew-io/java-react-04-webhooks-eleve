# Mémos 04 — Un événement est une affirmation à vérifier

## Le HMAC couvre des octets

Dans notre protocole, le code d’authentification porte l’horodatage et le corps exact. Le JSON est une représentation d’un objet ; deux représentations équivalentes peuvent différer octet par octet. La primitive `Mac` du JDK permet de calculer HmacSHA256 ; `MessageDigest.isEqual` compare les octets de vérification. Ces primitives ne définissent ni nos en-têtes ni notre règle de temps : ils sont des choix pédagogiques. [Mac](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/javax/crypto/Mac.html) · [MessageDigest](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/security/MessageDigest.html).

La clé du cours est publique. La démonstration d’un corps altéré rejeté montre que le calcul couvre ce corps ; elle ne prouve pas que l’émetteur représente une banque ou un partenaire autorisé. Le cours n’enseigne aucun secret de production.

## Quatre questions avant une transition

| Question | Contrôle de l’exercice |
|---|---|
| Ce corps correspond-il à sa signature de démo ? | HMAC sur octets et timestamp |
| Est-il suffisamment récent pour ce protocole ? | Écart absolu d’horloge ≤ 300 s |
| Parle-t-il de la bonne tentative et du bon montant ? | Référence, devise, montant, statut autorisé |
| A-t-il déjà été traité, et l’état accepte-t-il encore un résultat ? | eventId et machine à états |

Un événement peut réussir les trois premières questions et être un doublon. Un événement correctement signé peut aussi annoncer un montant incohérent. Les contrôles sont complémentaires.

## Déduplication et état doivent progresser ensemble

Inscrire « eventId traité » puis échouer avant le changement d’état peut perdre un résultat. Changer l’état puis échouer avant l’inscription peut répéter un effet. Le cours réunit les deux décisions dans une section atomique du processus. Ce choix n’apporte aucune récupération après crash ni coordination multi-instance ; on les mentionne pour situer la preuve.

## Une machine à états encode une règle métier

Le lab conserve le premier terminal reçu et validé. Il accepte PENDING vers SUCCEEDED ou FAILED, puis refuse toute régression. Cette règle donne un comportement observable et testable. Un vrai protocole pourrait comporter autorisation, capture, annulation, remboursement ou réconciliation : on ne les déduit pas de nos trois états.

## Polling et incertitude

Un timer est une ressource à arrêter. Une requête en cours ne doit pas se multiplier parce qu’un autre intervalle vient d’expirer. La page dispose d’un budget de lectures et affiche l’incertitude si ce budget est épuisé. Arrêter d’attendre n’est pas un événement métier d’échec. Le nettoyage d’effet garde la page cohérente lors d’un changement de référence. [useEffect](https://react.dev/reference/react/useEffect).

## Questions de compréhension

1. Pourquoi parser puis re-sérialiser le corps invalide-t-il certaines signatures ?
2. Un montant faux peut-il posséder une signature HMAC valide dans la démo ?
3. Quelle différence entre une requête idempotente et un événement dédupliqué ?
4. Quand faut-il inscrire l’eventId comme traité ?
5. Pourquoi la fin des 15 lectures ne doit-elle pas afficher FAILED ?
