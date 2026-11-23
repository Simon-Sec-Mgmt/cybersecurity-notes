[⬅ Retour à Bug Bounty Bootcamp](../01_The_Industry/00_General.md)

# Chapitre 8 — Clickjacking

> **Source :** Bug Bounty Bootcamp — Chapitre 8 · **Type :** note-chapitre  
> `#bug-bounty` `#cybersécurité` `#clickjacking` `#web-hacking`

## Résumé

Le clickjacking ("redressage d'interface") superpose une page invisible sur une page légitime pour piéger le clic de la victime. Repose sur la balise `<iframe>`. ⚠️ Souvent **hors scope** des programmes de bug bounty (nécessite beaucoup d'interaction utilisateur) — toujours vérifier la politique avant de tester.

## Concepts clés

### Mécanisme

Une iframe permet d'intégrer une page dans une autre :

```html
<iframe src="https://cible.example/transfer?to=attaquant&amount=5000" width="500" height="500"></iframe>
```

L'attaquant superpose cette iframe (rendue invisible) sur un décor inoffensif (ex: bouton "S'abonner à la newsletter"), en jouant sur le CSS :

```css
#iframe-victime {
  opacity: 0.00001;   /* rend l'iframe invisible mais cliquable */
  z-index: 1;         /* place l'iframe au-dessus du décor */
}
#decor {
  position: absolute; /* superpose exactement les deux éléments */
  z-index: -1;
}
```

Le bouton réel de la victime (ex: "Transférer") est aligné pixel pour pixel sur le bouton visible du décor (ex: "S'abonner"). Comme la victime est authentifiée sur le vrai site dans l'iframe, son clic déclenche l'action sensible sans qu'elle s'en rende compte.

### Conditions nécessaires

1. La page ciblée contient une **action qui change un état** (state-changing) — changement de mot de passe, transfert d'argent, etc.
2. La page peut être **framée** par un site tiers

### Prévention (à vérifier lors de la chasse)

|Mécanisme|Effet|
|---|---|
|`X-Frame-Options: DENY`|Interdit tout framing|
|`X-Frame-Options: SAMEORIGIN`|Autorise le framing seulement depuis la même origine|
|`Content-Security-Policy: frame-ancestors 'none'`|Équivalent moderne, plus flexible (peut lister des origines précises)|
|Cookie `SameSite=Strict` ou `Lax`|Le cookie de session n'est pas envoyé dans une requête déclenchée depuis une iframe tierce → la victime n'est pas authentifiée dans le contexte piégé|

### Méthodologie de chasse

1. Lister toutes les actions "state-changing" du site (changement de mdp, transfert, suppression de compte...) et leurs URLs
2. Vérifier qu'elles sont déclenchables **par simple clic** (pas de saisie clavier requise — le clickjacking ne peut forger que des clics)
3. Intercepter la réponse de chaque page sensible et vérifier la présence de `X-Frame-Options` / `Content-Security-Policy`
4. Confirmer avec une page de test :
    
    ```html
    <iframe src="URL_DE_LA_PAGE_CIBLE" width="500" height="500"></iframe>
    ```
    
    Si le contenu s'affiche → la page est frameable

### Bypass de protection clickjacking

- Le **frame-busting** (protection en JS plutôt qu'en en-tête HTTP) est peu fiable et souvent contournable. Un piège classique côté développeur :
    
    ```js
    if (top.location == self.location) { /* autoriser */ }
    ```
    
    → Ce test échoue à détecter un framing en 2 niveaux : si une fonctionnalité du site cible permet d'intégrer un iframe custom (partage de lien, embed vidéo...), l'attaquant peut framer sa page malveillante **à l'intérieur** d'une page du domaine victime, rendant `top.location` et `self.location` identiques ("double iframe trick")

### Escalade

- L'impact dépend entièrement de la criticité de l'action piégée (changer une couleur de thème vs transférer de l'argent)
- Chaînage possible : ex. deux clickjacking combinés — un pour changer l'email de facturation, un autre pour déclencher l'envoi d'un résumé de compte à cet email → fuite d'information
- Cas réel cité : un rapport HackerOne (Periscope, 2019) montrant qu'un `X-Frame-Options: ALLOW-FROM` (directive obsolète, non supportée par les navigateurs modernes) laissait la page de paramètres de compte frameable, permettant potentiellement de piéger une désactivation de compte

## Liens

- [Bug Bounty Bootcamp](../01_The_Industry/00_General.md)
- [Chapitre 7](07_Open_Redirects.md)
- Bypass de protection clickjacking
- SameSite (cookies)
