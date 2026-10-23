# GS SIGMA — déploiement GitHub + Render (sans Google Apps Script)

## Contenu
- `index.html` : interface GS SIGMA et aides intégrées.
- L’aide utilisateur est intégrée à l’interface et accessible depuis la connexion et le tableau de bord.
- `server.js` : serveur Node.js et API de synchronisation.
- `render.yaml` : configuration du service Render et de PostgreSQL.
- `sigma_logo.png` et `screenshots/` : ressources visuelles.

## Déploiement
1. Téléverse tous les fichiers de cette archive à la racine d’un dépôt GitHub.
2. Dans Render, crée un Blueprint depuis ce dépôt et vérifie la création du service Web et de la base PostgreSQL.
3. Attends la fin du déploiement puis vérifie `https://TON-SITE.onrender.com/api/health`. Le résultat attendu est `ok: true` et `database: connected`. Le site doit être ouvert depuis le service Web Render, pas depuis GitHub Pages ni en ouvrant index.html localement : ces options ne fournissent pas l’API PostgreSQL.
4. Ouvre le site et teste le bouton « Aide » depuis l’écran de connexion puis depuis le tableau de bord.
5. Vérifie l’interface sur un ordinateur et un téléphone.

## Aide utilisateur
L’aide est intégrée directement à l’interface : les étapes d’utilisation restent accessibles depuis l’écran de connexion et le tableau de bord. Le lien vers le guide PDF distant a été retiré pour éviter une option injoignable. Les exports PDF générés par les fonctions de l’application sont conservés.

## Synchronisation et sécurité
Le serveur utilise PostgreSQL via `DATABASE_URL`. Avant d’utiliser le site avec des données réelles, l’authentification et les permissions doivent être sécurisées côté serveur. Ne publie pas de mots de passe réels dans un service dont l’API de base partagée est encore ouverte. Les données sauvegardées uniquement dans le stockage local d’un ancien navigateur ne sont pas importées automatiquement : exporte-les et importe-les via les fonctions prévues, si disponibles.


## Correction PDF introuvable

## Page 404 et mode maintenance
- `404.html` est la page de secours affichée lorsqu'une adresse du site n'existe pas (HTTP 404). Elle explique à l'utilisateur que la page est introuvable.
- Pour afficher cette page comme message de maintenance à tous les visiteurs, ouvre Render → service Web → Environment, ajoute `MAINTENANCE_MODE` avec la valeur `true`, puis redéploie/redémarre le service. Le site renverra alors HTTP 503 et conservera `/api/health` accessible pour le contrôle technique.
- Pour remettre le site en service, change `MAINTENANCE_MODE` en `false` ou supprime cette variable, puis redémarre le service.
- La page est autonome et ne dépend d'aucune image ni ressource externe.

## Mise à jour — choix des UE et accès Wi-Fi campus
- Le menu **Choix des UE (étudiants)** est placé dans la section Gestion et reste accessible aux comptes étudiants/utilisateurs. Ils peuvent cocher leurs UE, enregistrer leur sélection et exporter le récapitulatif PDF.
- Les paramètres officiels du catalogue et la limite officielle de crédits restent réservés à l’administration dans l’interface.
- Dans **Administration → Comptes utilisateurs → Modifier**, l’administrateur peut renseigner l’identifiant et le mot de passe Wi-Fi attribués par l’école/campus.
- Chaque utilisateur retrouve ces informations dans **Mon compte / Wi-Fi campus**. Il peut les afficher ou copier les identifiants, mais pas les modifier.

**Sécurité importante :** dans cette version, l’application synchronise encore une partie importante des données au moyen d’un état partagé côté navigateur. Les contrôles de rôle de l’interface ne constituent donc pas à eux seuls une protection serveur suffisante pour des secrets Wi-Fi réels. Avant d’y saisir les vrais mots de passe du campus, il faut renforcer l’API avec une authentification serveur et des autorisations par rôle/compte, afin qu’un utilisateur ne puisse jamais télécharger les identifiants des autres.


## Dépannage — « Base distante inaccessible »
- Ouvre directement `https://TON-SITE.onrender.com/api/health`. Si cette adresse affiche une page 404 ou HTML, vérifie que tu utilises le domaine du service Web Render et que `server.js` a été déployé. Si le site est ouvert depuis GitHub Pages ou depuis un fichier HTML local, son API Render ne sera pas accessible à l’adresse relative `/api/...`.
- Si la réponse est HTTP 503 avec `database: unavailable`, ouvre Render → service Web → Environment et vérifie que `DATABASE_URL` est bien liée à la base PostgreSQL associée. Vérifie aussi que la base n’est pas suspendue et que les journaux du service ne montrent pas une erreur de connexion.
- Si `/api/health` répond `ok: true` mais que le site affiche encore l’erreur, force l’actualisation du navigateur (Ctrl+F5) et vérifie que le dernier déploiement est terminé.
- Le service Node démarre même si `DATABASE_URL` manque : l’interface peut s’ouvrir et `/api/health` indique alors clairement que PostgreSQL n’est pas configurée. La page de connexion affiche une indication selon le type d’erreur HTTP/réseau.


Interface : les boutons « Aide et guide utilisateur » et « Télécharger le guide en PDF » ont été retirés de l’écran de connexion et de la navigation. L’aide/guide intégré est masqué.
