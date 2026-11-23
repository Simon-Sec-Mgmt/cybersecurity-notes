[⬅ Retour à Bug Bounty Bootcamp](../01_The_Industry/00_General.md)

# Chapitre 10 — Insecure Direct Object References (IDOR)

> **Source :** Bug Bounty Bootcamp — Chapitre 10 · **Type :** note-chapitre  
> `#bug-bounty` `#cybersécurité` `#idor` `#web-hacking` `#controle-acces`

## Résumé

Un IDOR = un contrôle d'accès manquant. L'application accède directement à une ressource (message, fichier, enregistrement) en se basant sur un identifiant fourni par l'utilisateur, sans vérifier que cet utilisateur a le droit d'y accéder.

## Concepts clés

### Mécanisme

```
https://cible.example/messages?user_id=1234   ← mes messages
https://cible.example/messages?user_id=1233   ← messages d'un autre, sans autorisation !
```

Ne se limite pas à la lecture : un IDOR peut aussi permettre de **modifier** ou **supprimer** la ressource d'un autre utilisateur (ex: `POST /change_password` avec un `user_id` arbitraire), ou d'accéder à des **fichiers système** via un chemin prévisible (→ lié au directory traversal, ex: lire `/etc/shadow`).

### Deux causes

1. Absence de vérification d'identité/permissions côté serveur
2. Identifiants d'objets **prévisibles** (IDs séquentiels, numériques)

### Prévention (donc à contourner en tant que hunter)

- Vérifier que l'utilisateur authentifié correspond bien à l'ID demandé
- Utiliser des identifiants non devinables (hash, clé aléatoire) — mais **incomplet seul** : si l'URL/clé fuite ailleurs, l'IDOR redevient exploitable

### Méthodologie de chasse

1. **Créer deux comptes** (idéalement à chaque niveau de privilège) — un "attaquant", un "victime" — pour observer objectivement si un changement d'ID sur le compte A affecte les données visibles/modifiables du compte B. Tester aussi en **non authentifié**
2. **Cartographier les fonctionnalités** qui renvoient ou modifient des données utilisateur
3. **Capturer les requêtes** via proxy (Burp) en session sur les deux comptes en parallèle (ex: Firefox = compte A, Chrome = compte B) — chercher IDs dans paramètres URL, corps de requête, headers, cookies, chemins de fichier
4. **Échanger les IDs** et observer si l'accès est accordé

### Bypass de protection IDOR — au-delà de l'ID numérique simple

- **IDs encodés/hashés** — toujours essayer de décoder une chaîne "aléatoire" (base64, base64url, hex, URL encoding). Si l'ID en clair est prévisible une fois décodé, on peut encoder son propre ID falsifié
- **Entropie insuffisante** — créer plusieurs comptes pour analyser un pattern dans la génération des IDs "randomisés"
- **IDs qui fuitent ailleurs** — ex: un `conversation_id` bien aléatoire, mais listable publiquement via le `user_id` (lui-même visible sur le profil public) → chaînage
- **Fournir un ID que l'appli ne demande pas** — certains endpoints identifient l'utilisateur via le cookie de session, mais acceptent _aussi_ un paramètre `user_id`/`id` explicite en legacy/debug :
    
    ```
    GET /api_v1/messages                      → mes messages (via cookie)GET /api_v1/messages?user_id=AUTRE_ID     → à tester quand même !
    ```
    
- **Blind IDOR** — la réponse HTTP ne change pas, mais la fuite se produit ailleurs (email envoyé à l'utilisateur courant contenant les données d'un autre, export différé, rapport mensuel...)
- **Changer la méthode HTTP** — un GET protégé n'implique pas que DELETE/PUT/PATCH le soient aussi sur la même ressource
- **Changer le type de fichier demandé** — ajouter `.json` à une requête bloquée peut parfois faire emprunter un chemin de code différent, moins bien protégé :
    
    ```
    GET /get_receipt?receipt_id=2983       (bloqué)GET /get_receipt?receipt_id=2983.json  (à tester)
    ```
    

### Escalade

- Prioriser les fonctionnalités critiques : réinitialisation/changement de mot de passe, récupération de compte (impact business max)
- Chaînage : IDOR en écriture + self-XSS → stored XSS ; IDOR sur reset password + énumération de noms d'utilisateur → prise de masse de comptes ; IDOR en écriture sur un compte admin → potentiellement RCE

### Automatisation

- Burp Intruder pour itérer sur une plage d'IDs
- Extensions Burp dédiées : **Autorize** (teste l'accès avec un compte moins privilégié aux ressources d'un compte plus privilégié), **Auto Repeater**, **AuthMatrix**

## Liens

- [Bug Bounty Bootcamp](../01_The_Industry/00_General.md)
- [Chapitre 9](09_Cross_Site_Request_Forgery_%28CSRF%29.md)
- Path Traversal
- Blind IDOR
- Stored XSS
