[⬅ Retour à Bug Bounty Bootcamp](../01_The_Industry/00_General.md)

# Chapitre 9 — Cross-Site Request Forgery (CSRF)

> **Source :** Bug Bounty Bootcamp — Chapitre 9 · **Type :** note-chapitre  
> `#bug-bounty` `#cybersécurité` `#csrf` `#web-hacking` `#owasp-top10`

## Résumé

Le CSRF force le navigateur d'une victime authentifiée à envoyer une requête à son insu, en exploitant le fait que les cookies de session sont automatiquement joints à chaque requête, peu importe le site qui l'initie. Cible les actions "state-changing" (pas les requêtes de lecture — l'attaquant ne peut pas lire la réponse).

## Concepts clés

### Mécanisme

Une page malveillante contient un formulaire qui pointe vers le site cible :

```html
<html>
  <iframe style="display:none" name="csrf-frame">
    <form method="POST" action="https://cible.example/send_tweet" target="csrf-frame" id="csrf-form">
      <input type="text" name="content" value="message forgé">
    </form>
  </iframe>
  <script>document.getElementById("csrf-form").submit();</script>
</html>
```

La victime charge cette page → son navigateur envoie automatiquement son cookie de session → le serveur traite la requête comme légitime.

### Prévention

|Mécanisme|Fonctionnement|
|---|---|
|**CSRF Token**|Chaîne aléatoire unique par session/formulaire, embarquée dans chaque form et vérifiée côté serveur|
|**Cookie `SameSite=Strict`**|Le cookie n'est jamais envoyé en cross-site|
|**Cookie `SameSite=Lax`**|Le cookie est envoyé seulement lors d'une navigation de premier niveau (clic sur un lien), pas pour un POST/iframe/image cross-site — **défaut de Chrome depuis 2020**|

⚠️ SameSite=Lax n'empêche pas tout : un endpoint state-changing accessible en **GET** reste exploitable (le clic déclenche une navigation top-level), et certains sites forcent `SameSite=None` pour des besoins d'intégration tierce.

### Méthodologie de chasse

1. Lister toutes les actions state-changing (changement mdp, envoi mail, suppression...) et leurs endpoints
2. Vérifier l'absence (ou la faiblesse) de protection CSRF dans la requête interceptée — chercher "csrf"/"state" dans les headers, cookies, params
3. Confirmer avec un formulaire HTML auto-submit imitant la requête légitime, à ouvrir dans un navigateur connecté (⚠️ tester avec Firefox, pas Chrome, qui protège par défaut via SameSite)

### Bypass de protection CSRF

- **Exploiter un clickjacking** — si la page est framable, on obtient le même résultat qu'un CSRF via un clic piégé
- **Changer la méthode HTTP** — un endpoint protégé en POST peut être vulnérable si la même action accepte aussi le GET (souvent non protégé) :
    
    ```html
    <img src="https://cible.example/password_change?new_password=abc123"/>
    ```
    
- **Token vide ou absent** — bug de logique fréquent : le serveur ne valide le token que **s'il est présent**
    
    ```python
    def process_state_changing_action():    if request.csrf_token:      # ← validation seulement si le champ existe        validate_token()    execute_action()
    ```
    
    → envoyer la requête sans le paramètre `csrf_token`, ou avec une valeur vide, suffit parfois
- **Token d'une autre session** — si le serveur vérifie juste que le token existe dans une liste de tokens valides (sans le lier à l'utilisateur courant), on peut utiliser son propre token dans l'attaque contre la victime
- **Double-submit cookie** — protection qui compare juste "cookie == paramètre de requête", sans registre serveur. Un attaquant qui peut fixer le cookie de la victime (via _session fixation_) peut mettre la même valeur bidon des deux côtés pour passer la vérification
- **Contournement du contrôle `Referer`** :
    - Supprimer l'en-tête entièrement via `<meta name="referrer" content="no-referrer">` — marche si le serveur ne valide que quand l'en-tête est présent
    - Placer le domaine cible en sous-domaine (`example.com.attaquant.com`) ou en chemin (`attaquant.com/example.com`) si le check ne fait qu'un `"example.com" in referer`
- **XSS bat toujours CSRF** — un XSS permet de voler le vrai token et de forger la requête via `XMLHttpRequest`/`fetch`

### Escalade via CSRF

- **Fuite d'info** — changer l'email de facturation d'un utilisateur via CSRF, puis déclencher l'envoi d'un résumé de compte → exfiltration de données personnelles
- **Self-XSS → Stored XSS** — un champ vulnérable au self-XSS (que seul l'utilisateur peut normalement modifier) devient exploitable à distance si son endpoint de modification est vulnérable au CSRF : l'attaquant force l'écriture du payload XSS dans le champ de la victime
- **Prise de compte totale** — CSRF sur un endpoint de définition de mot de passe (ex: comptes créés via SSO social, sans mot de passe existant) permet d'assigner un mot de passe connu de l'attaquant

### Livraison du payload

- Lien vers un site externe hébergeant un formulaire auto-submit
- `<img src="...">` pour les CSRF déclenchables en GET (fonctionne même dans un forum, une pièce jointe email, etc.)
- Combiné à un stored XSS pour toucher tous les visiteurs d'une page piégée automatiquement

## Liens

- [Bug Bounty Bootcamp](../01_The_Industry/00_General.md)
- [Chapitre 8](08_Clickjacking.md)
- Stored XSS
- SameSite (cookies)
- Bypass de protection clickjacking
- CSRF Token
