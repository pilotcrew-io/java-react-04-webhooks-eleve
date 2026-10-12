# Lab 04 — Des événements de paiement vérifiables

**4 h 30 · Java et React.** Après acceptation de la tentative, un système externe pourrait annoncer un résultat. Le laboratoire remplace ce système par un émetteur local. Un événement peut arriver deux fois, être ancien ou contredire un état déjà terminal. Construire un traitement qui ne se contente pas de recopier le dernier JSON reçu.

## Protocole de démonstration à respecter

Le destinataire est `POST /api/webhooks/mock-psp`. La clé publique de démonstration est `pilotcrew-local-demo-key`. Les en-têtes sont `X-Demo-Timestamp` (secondes Unix, chiffres ASCII) et `X-Demo-Signature` (64 caractères hexadécimaux de HMAC-SHA256). Le message signé est la concaténation des octets ASCII de l’horodatage, d’un point, puis des **octets exacts du corps reçu**.

```text
message signé = timestamp + "." + corps HTTP brut
```

Corps d’un événement fictif :

```json
{
  "eventId": "evt-demo-001",
  "paymentId": "REFERENCE_RETOURNEE_PAR_VOTRE_API",
  "status": "SUCCEEDED",
  "amountMinor": 2500,
  "currency": "EUR"
}
```

Seuls SUCCEEDED et FAILED sont des résultats d’événement acceptés. `eventId` est non vide et borné ; `paymentId` désigne une tentative existante. Montant et devise doivent être égaux à ceux de la tentative. Le même événement logique peut être signé avec un horodatage neuf pour être rejoué.

| Cas | Réponse / effet attendu |
|---|---|
| Signature absente, mal formée ou invalide ; écart d’horloge supérieur à 300 s | 401 ; aucun changement |
| Corps mal formé, propriétés non prévues ou valeur non supportée | 400 ; aucun changement |
| Tentative inconnue | 404 ; aucun changement |
| Montant/devise incohérents, ou eventId déjà associé à une autre charge logique | 409 ; aucun changement |
| Événement inédit cohérent, tentative PENDING | 200 `{"outcome":"APPLIED"}` ; état terminal appliqué |
| Même eventId et même charge logique déjà traités | 200 `{"outcome":"DUPLICATE"}` ; état inchangé |
| Nouvel événement cohérent après un état terminal | 200 `{"outcome":"IGNORED"}` ; état terminal conservé |

Le premier résultat terminal cohérent est conservé dans ce modèle pédagogique. Une règle métier réelle peut être différente. Une signature valide ne rend pas automatiquement le montant correct et ne rend pas un événement nouveau.

## 1. Prévoir les arrivées difficiles — 20 min

Tracer `PENDING → SUCCEEDED` et `PENDING → FAILED`. Aucun chemin ne ramène un terminal à PENDING ni ne remplace un terminal par son opposé. Écrire quatre expériences : résultat valide, même événement deux fois, événement opposé plus tard, montant faux mais signature correcte.

Distinguer trois identifiants : clé d’idempotence de la demande initiale, référence de paiement et eventId. Ils ne se remplacent pas : deux événements différents peuvent viser le même paiement et une livraison identique peut réutiliser le même eventId.

## 2. Vérifier ce qui a vraiment été reçu — 60 min

Créer un petit émetteur local, en Python standard ou Java, qui construit le corps en octets, calcule la signature et envoie **ces mêmes octets**. Aucun SDK PSP ni compte n’est nécessaire. Prévoir au minimum le choix de paymentId, statut, montant et eventId. La référence ne doit jamais être un paiement d’un autre système.

Côté Java, recevoir le corps brut avant sa transformation en objet. Lire l’horodatage, vérifier qu’il est exploitable et dans la fenêtre ±300 secondes à l’aide d’une `Clock` injectée. Calculer HmacSHA256 avec la primitive du JDK ; décoder la signature hexadécimale, vérifier sa longueur et comparer les octets avec l’API dédiée. Une entrée mal formée produit une réponse contractuelle, pas une exception exposée.

La désérialisation métier intervient après ces contrôles. Ne parser puis re-sérialiser le JSON pour reconstruire la signature : espace, saut de ligne ou ordre des propriétés peuvent changer les octets signés. Le protocole pédagogique signe aussi l’horodatage pour éviter qu’un appelant ne prolonge la fraîcheur sans recalculer le HMAC.

Premier checkpoint : envoyer un événement valide, puis changer un caractère du corps en conservant l’ancienne signature. Le second essai donne 401 et ne modifie pas la tentative. La clé étant publique, une personne peut évidemment recalculer une signature : on observe une propriété technique, pas une authentification bancaire.

## 3. Dédupliquer et appliquer ensemble — 65 min

Désérialiser et valider les champs après la signature. Rechercher la tentative et comparer montant/devise. Conserver pour chaque eventId la charge logique validée `(paymentId, status, amountMinor, currency)` et l’issue du traitement. Des différences d’espace JSON ne changent pas cette charge logique si la signature de chaque corps est valide.

Dans une même opération protégée, vérifier le registre d’événements, décider la transition et inscrire l’événement. Pour un doublon identique, retourner DUPLICATE. Pour un eventId réutilisé avec un autre contenu logique, retourner 409. Un nouvel eventId sur un état déjà terminal est marqué traité et renvoie IGNORED ; son rejeu identique renverra ensuite DUPLICATE.

Ne marquer pas comme traité un événement rejeté avant validation. Un événement de montant faux peut être corrigé et renvoyé sous la même référence s’il n’a jamais été accepté. Dans les logs, conserver seulement des références fictives et une issue : eventId, paymentId, état avant, état après, APPLIED/DUPLICATE/IGNORED. Ne journaliser ni corps brut complet ni clé.

## 4. Tester l’ordre, le temps et l’altération — 55 min

Injecter une horloge fixe dans les tests de signature et d’ancienneté. Tester exactement la limite de 300 secondes, puis 301 secondes dans le passé et le futur. Aucun test n’attend réellement cinq minutes. Prévoir des helpers de test distincts pour signer et pour altérer un corps déjà signé.

Vérifier au minimum : événement valide, signature absente, signature fausse, corps altéré, JSON signé invalide, horodatage expiré, paiement inconnu, montant incorrect pourtant signé, doublon, eventId réutilisé, et événement terminal opposé tardif. Pour le doublon concurrent, deux appels portent le même événement : attendre une issue APPLIED et une DUPLICATE, une seule transition et le même état final.

Pour le cas désordonné, créer une nouvelle tentative, appliquer FAILED, puis envoyer SUCCEEDED avec un autre eventId ; notre règle conserve FAILED et renvoie IGNORED. Expliquer pourquoi c’est un choix du cours à faire valider au métier dans un autre contexte.

## 5. Donner à React un suivi borné — 50 min

Après création ou reprise d’une tentative, faire lire `GET /api/payments/{id}` immédiatement puis toutes les 2 secondes tant que le résultat est PENDING, avec **15 lectures au maximum** et une seule lecture à la fois. Programmer la lecture suivante après la fin de la précédente, pas dans un intervalle qui empile des requêtes. Nettoyer timer et requête au changement de référence et au démontage.

Une erreur réseau conserve le dernier statut connu et propose de relancer la consultation. La fin du budget de lectures affiche « Résultat encore en attente — consulter à nouveau » ; elle ne transforme pas le paiement en FAILED. Un état terminal arrête les lectures. Une réponse tardive d’un paiement précédent n’écrase pas la référence active.

Tester le suivi avec timers contrôlés et réponses déterministes : PENDING puis SUCCEEDED, PENDING jusqu’à la borne, erreur de lecture et démontage. Avec le serveur réel, créer une tentative dans React, envoyer son événement local, puis vérifier l’arrivée du texte terminal et l’arrêt des requêtes dans Réseau.

## 6. Relier les preuves — 20 min

Rendre une trace contenant création, référence, eventId, issue serveur et état affiché. Rejouer un doublon devant le pair et montrer qu’aucune nouvelle transition n’apparaît. Exécuter les suites puis les builds et joindre les rapports.

Conclure le journal avec trois limites précises : clé publique, stockage non durable, règle du premier terminal. L’extension hors temps est une conception d’inbox transactionnelle durable ; ne pas la présenter comme déjà réalisée par le verrou mémoire.

## Déroulé minuté — 270 minutes

| Plage | Activité | Durée |
|---|---|---:|
| 00:00–00:20 | Écrire le protocole local et la table des transitions | 20 min |
| 00:20–01:20 | Construire l’émetteur local et vérifier HMAC brut / horodatage | 60 min |
| 01:20–02:25 | Valider cohérence, déduplication et transitions atomiques | 65 min |
| 02:25–03:20 | Tester événements altérés, expirés, répétés et désordonnés | 55 min |
| 03:20–04:10 | Créer un suivi React avec polling borné et nettoyage | 50 min |
| 04:10–04:30 | Démontrer une trace corrélée et expliquer les limites | 20 min |

## Contrat commun du fil rouge

La boutique **PilotCrew Démo** ne vend rien. Elle présente le produit `demo-ticket`, libellé « Atelier fictif », à `unitAmountMinor: 2500`, devise `EUR`, `fictional: true`. Une quantité entière de **1 à 5** produit une commande dont le montant est calculé par le serveur. Dans cette simulation EUR seulement, 2500 unités mineures s’affichent 25,00 € fictifs. Aucun autre nombre de décimales ni aucune conversion de devise n’est traité.

| Objet | Champs publics attendus |
|---|---|
| Product | `id`, `label`, `unitAmountMinor`, `currency`, `fictional` |
| Order | `id`, `productId`, `quantity`, `amountMinor`, `currency`, `fictional` |
| Payment | `id`, `orderId`, `amountMinor`, `currency`, `method`, `status`, `fictional` |

Le corps de création de commande accepte uniquement `productId` et `quantity`. Le corps de création de paiement accepte uniquement `method`. Un montant, une devise ou un statut ajoutés par le client ne doivent jamais devenir une autorité : **ce contrat les refuse en 400**. Le code utilise les identifiants retournés, jamais un identifiant d’exemple comme s’il existait déjà.

| Route | Résultat contractuel |
|---|---|
| `GET /api/products` | 200, tableau de produits fictifs |
| `POST /api/orders` | 201, commande créée et en-tête Location |
| `GET /api/orders/{id}` | 200, commande ; 404 si référence absente |
| `POST /api/orders/{id}/payments` | À partir du lab 03 : 202, tentative PENDING ; Idempotency-Key obligatoire |
| `GET /api/payments/{id}` | À partir du lab 03 : 200, état serveur ; 404 si référence absente |
| `POST /api/webhooks/mock-psp` | À partir du lab 04 : réception d’un événement signé par le simulateur local |

Les erreurs structurées emploient `application/problem+json` et au moins `type`, `title`, `status`, `detail`, `code`. `code` est une extension de ce projet. Le texte est destiné à l’utilisateur ; aucune pile d’exception ne sort dans la réponse. Les codes HTTP du cours sont des choix de contrat précis ; ils ne décrivent pas toutes les API de paiement existantes.

Toutes les données métier sont en mémoire dans une JVM : un redémarrage les efface. Cette limite est visible dans la documentation et dans les scénarios. Le cours ne met en place ni identité client, ni base durable, ni gestion de stock, ni remboursement, ni autorisation financière réelle.
