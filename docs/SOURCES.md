# Sources officielles et choix du laboratoire

Pages primaires consultées le **12 octobre 2026**. Les tutoriels, scénarios, contrats et barèmes sont des créations pédagogiques originales de PilotCrew. Les pages ci-dessous documentent les outils ou principes utilisés ; elles ne décrivent pas notre mock PSP.

| Source primaire | Usage dans le cours |
|---|---|
| [Oracle Java 21 — Mac](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/javax/crypto/Mac.html) | Primitive HmacSHA256 appliquée au protocole local inventé pour le cours. |
| [Oracle Java 21 — MessageDigest](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/security/MessageDigest.html) | Comparaison des octets de signature avec isEqual. |
| [React — useEffect](https://react.dev/reference/react/useEffect) | Nettoyage des effets, dépendances et protection contre les réponses tardives. |
| [Spring Framework — MockMvc](https://docs.spring.io/spring-framework/reference/testing/mockmvc.html) | Périmètre des tests MVC sans serveur HTTP externe. |
| [IETF — RFC 9110](https://www.rfc-editor.org/rfc/rfc9110.html) | Sémantique HTTP ; distinguer réponse acceptée et résultat métier. |

Les versions et noms d’API pouvant évoluer, l’encadrant vérifie les liens et la combinaison exacte avant une nouvelle promotion. Java 21 et Spring Boot 4.1.1 sont fixés pour ce cursus ; les versions JavaScript sont celles du package-lock.json validé. La version de JUnit est gérée par le parent Spring Boot ; le cours ne prescrit pas de mélanger une ancienne configuration de tests avec Boot 4.

La répartition Java / React, les interfaces de paiement, l’intégration et la rigueur sont des thèmes issus de [l’annonce de référence](https://consultant.jean-michel.io/annonces/31738). Le choix Spring Boot, TypeScript, Vite, tests et protocole HMAC local appartient à la conception pédagogique. Aucune source ne transforme les 27 heures de ce parcours en expérience senior.
