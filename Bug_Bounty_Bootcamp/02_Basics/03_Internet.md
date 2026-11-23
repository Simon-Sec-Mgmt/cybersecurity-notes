[⬅ Retour à Bug Bounty Bootcamp](../01_The_Industry/00_General.md)

# Chapitre 3 — Comment fonctionne internet

> **Source :** Bug Bounty Bootcamp — Chapitre 3 · **Type :** note-chapitre  
> `#bug-bounty` `#cybersécurité` `#réseau` `#http` `#jwt`

## Résumé

Bases indispensables avant de chasser des bugs : modèle client-serveur, DNS, ports, structure des requêtes/réponses HTTP, encodages, gestion de session, authentification par token et JWT, et introduction à la Same-Origin Policy.

## Concepts clés

### Modèle client-serveur

- Le navigateur (client) demande des ressources ; le serveur les fournit
- Une page web = assemblage de HTML (structure), CSS (style), JS (interactivité), + ressources média
- Les API permettent aussi à des applications de s'échanger des données entre elles (approfondi au chapitre 24)

### DNS

- Traduit un nom de domaine en adresse IP (IPv4 type `93.184.216.34`, IPv6 type `2001:db8::1`)
- Fonctionne comme un "bottin téléphonique" d'internet

### Ports réseau

- Identifient un service précis sur une machine (0 à 65535)
- Port 80 = HTTP, port 443 = HTTPS par défaut

### Requêtes et réponses HTTP

Structure d'une requête HTTP : ligne de requête + en-têtes + corps optionnel.

```
GET /profile HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0
Cookie: session=abc123
```

- **Méthodes courantes** : GET (lire), POST (soumettre), PUT (modifier), DELETE (supprimer), OPTIONS (méthodes autorisées)
- **En-têtes utiles à connaître** : `Host`, `User-Agent`, `Cookie`, `Referer`, `Authorization`

Structure d'une réponse :

```
HTTP/1.1 200 OK
Content-Type: text/html
Set-Cookie: session=abc123

<html>...</html>
```

- **Codes de statut** : 2xx = succès, 3xx = redirection, 4xx = erreur client (ex: 403 = accès interdit → signal potentiel de contrôle d'accès défaillant), 5xx = erreur serveur
- **En-têtes de réponse à surveiller** : `Set-Cookie`, `Location`, `Access-Control-Allow-Origin` (lié à Cross-Origin Resource Sharing (CORS)), `Content-Security-Policy`, `X-Frame-Options` (lié au [Clickjacking](../03_Web_Vulnerabilities/08_Clickjacking.md))

### Encodage de contenu

Trois encodages à connaître pour décoder ce qu'on observe en interception :

- **Base64** — encode des données binaires en texte imprimable (alphabet A-Z, a-z, 0-9, +, /, padding `=`)
- **Hex** — représentation base-16, plus lisible mais plus long
- **URL encoding** — remplace les caractères spéciaux par `%XX` (valeur hexadécimale)
- Outils pratiques : le décodeur intégré de Burp Suite, ou CyberChef

### Gestion de session et cookies

- Le serveur crée une session à la connexion et attribue un **ID de session** imprévisible
- Le navigateur stocke cet ID dans un cookie et le renvoie à chaque requête
- La déconnexion invalide la session côté serveur

### Authentification par token et JSON Web Token (JWT)

Contrairement à la session (état stocké côté serveur), le token contient l'information d'identité directement — le serveur n'a qu'à le valider.

Un JWT a 3 parties séparées par des points : `header.payload.signature`, chacune encodée en base64url.

```
{"alg":"HS256","typ":"JWT"}          → header
{"user":"alice","role":"user"}       → payload
<signature calculée avec une clé secrète>
```

**Vecteurs d'attaque JWT classiques :**

- **`alg: none`** — si l'application ne force pas la vérification de signature, un token avec un champ `alg` à `none` et une signature vide peut être accepté tel quel
- **Confusion d'algorithme (RSA → HMAC)** — si l'appli passe d'une vérification par clé publique RSA à HMAC, un attaquant peut signer un faux token avec la clé publique (connue de tous) utilisée comme clé HMAC
- **Brute-force de la clé secrète** — possible si la clé HMAC est trop faible
- **Fuite de la clé secrète** — via une autre faille (XXE, SSRF, directory traversal) qui permet de lire le fichier contenant la clé
- **Lecture d'info sensible** — un JWT non chiffré est juste encodé, pas confidentiel : n'importe qui peut le décoder en base64 et lire le payload

### Same-Origin Policy (SOP) — introduction

- Règle : un script de la page A ne peut accéder aux données de la page B que si elles ont la **même origine**
- Origine = protocole + hôte + port, les 3 doivent correspondre
- Exemple : `https://site.com` et `http://site.com` sont des origines **différentes** (protocole différent) ; `https://site.com` et `https://site.com:8080` aussi (port différent)
- Approfondi au chapitre 19 avec les vulnérabilités qui contournent cette protection

## Liens

- [Bug Bounty Bootcamp](../01_The_Industry/00_General.md)
- [Chapitre 2](../01_The_Industry/02_Reporting.md)
- Burp Suite
- JSON Web Token (JWT)
- Same-Origin Policy (SOP)
- Cross-Origin Resource Sharing (CORS)
- API
