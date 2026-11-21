# Sécuriser Didi's MC Store (à faire une seule fois, ~10 minutes)

Pourquoi : avant, n'importe qui connaissant l'adresse de la base pouvait lire les commandes
(noms, téléphones, adresses) et modifier ou supprimer les produits, et le mot de passe admin
se vérifiait dans le navigateur. Maintenant l'administrateur se connecte avec un vrai compte
Firebase, et la base refuse tout ce qui n'est pas autorisé.

## Ce que les règles garantissent
- Les clients peuvent seulement VOIR les produits et le réglage du site.
- Un client peut CRÉER une commande (format et longueurs contrôlés), jamais la lire,
  la modifier ni la supprimer.
- Seul l'administrateur connecté peut lire les commandes et modifier produits, catégories,
  image d'accueil et réglages.

## Étapes (dans l'ordre)
1. Console Firebase (console.firebase.google.com) > projet **didi-s-mc-store-3a0a1**.
2. **Authentication** > Commencer > onglet *Méthode de connexion* > **E-mail/Mot de passe** > Activer > Enregistrer.
3. **Authentication** > onglet *Utilisateurs* > **Ajouter un utilisateur** : votre e-mail + un mot de passe
   long (12 caractères minimum, jamais utilisé ailleurs). C'est l'identifiant de connexion de l'admin.
4. **Realtime Database** > onglet **Règles** > supprimez tout, collez le contenu du fichier
   `database.rules.json` > **Publier**.
5. Remplacez `index.html` sur Netlify par la nouvelle version (faites les étapes 4 et 5 en même temps :
   l'ancienne version du site ne fonctionne plus avec ces règles).
6. Ouvrez le site > icône utilisateur > connectez-vous avec l'e-mail et le mot de passe de l'étape 3.
   Le téléphone reste connecté jusqu'à « Déconnexion ».

## Protections à ajouter (fortement conseillé)
- **Restreindre la clé API** : console.cloud.google.com > APIs et services > Identifiants >
  clé « Browser key » du projet > *Restrictions relatives aux applications* > Sites Web >
  ajoutez `smcstore.netlify.app/*` (et votre domaine si vous en avez un).
- **App Check** (Firebase > App Check) : bloque les robots qui enverraient de fausses commandes.
- **Alertes budget** : Google Cloud > Facturation > Budgets, pour être prévenu en cas d'abus.

## Bonnes pratiques
- Ne partagez jamais le mot de passe admin ; déconnectez-vous d'un téléphone prêté ou perdu
  (Firebase > Authentication > utilisateur > Réinitialiser le mot de passe).
- Les anciennes règles étaient ouvertes et l'ancien code admin se trouvait dans la page :
  considérez-le comme compromis, il n'est plus utilisé.
- Les téléphones des clients restent confidentiels : seule la connexion admin permet de les lire.

## Fidélité et parrainage (mise à jour des règles)
Les règles de `database.rules.json` ont été complétées : republiez-les (Realtime Database > Règles > Publier)
en même temps que le nouvel `index.html`.
- `customers` (clients, téléphones, crédits) : lecture et écriture réservées à l'administrateur connecté.
- `cards` (carte publique d'un client : prénom, tampons, crédit) : lisible uniquement si l'on connaît le code
  exact (impossible de lister les cartes), modifiable seulement par l'administrateur.
- Les réductions annoncées par un client sont revérifiées par l'administrateur à la confirmation
  (le site recalcule à partir du dossier client et corrige le montant si besoin).