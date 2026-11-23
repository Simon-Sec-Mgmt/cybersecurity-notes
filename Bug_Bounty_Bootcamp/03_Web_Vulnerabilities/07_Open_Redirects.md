[⬅ Retour à Bug Bounty Bootcamp](../01_The_Industry/00_General.md)

# Chapitre 7 — Open Redirects

> **Source :** Bug Bounty Bootcamp — Chapitre 7 · **Type :** note-chapitre  
> `#bug-bounty` `#cybersécurité` `#open-redirect` `#web-hacking`

## Résumé

Un open redirect survient quand un attaquant peut manipuler un paramètre de redirection pour envoyer la victime vers un site externe malveillant. Bug souvent jugé "mineur" isolément, mais très utile en **chaînage** (phishing crédible, contournement d'allowlist SSRF, exfiltration de tokens via l'en-tête `Referer`).

## Concepts clés

### Mécanisme

Cas classique : redirection post-login vers la page initialement demandée.

```
https://example.com/login?redirect=https://example.com/dashboard
```

Si le paramètre n'est pas validé, un attaquant peut le remplacer :

```
https://example.com/login?redirect=https://attaquant.example
```

→ La victime clique un lien qui _semble_ pointer vers le site légitime, mais finit redirigée ailleurs.

**Variante : open redirect basé sur le `Referer`** — certains sites redirigent automatiquement vers l'URL contenue dans l'en-tête `Referer` (ex: après login/logout). Un attaquant héberge une page avec un simple lien vers le site cible pour forcer ce comportement.

### Méthodologie de chasse

1. **Repérer les paramètres de redirection** — noms fréquents : `redirect`, `redir`, `next`, `url`, `return`, `dest`, `forward`, `RelayState`... (pas toujours explicites) ; repérer aussi les réponses 3xx (301/302) sans paramètre visible → candidats à un redirect basé sur `Referer`
2. **Dorks Google** pour élargir la recherche :
    
    ```
    inurl:%3Dhttp site:cible.com        (paramètre contenant une URL absolue)inurl:redirect site:cible.cominurl:returnurl site:cible.com
    ```
    
3. **Tester** en injectant un hostname qu'on contrôle dans chaque paramètre trouvé
4. **Tester le cas `Referer`** en hébergeant une page avec un lien vers la cible

### Bypass de protection open redirect

La racine du problème : parser une URL correctement est difficile. Composants d'une URL :

```
scheme://userinfo@hostname:port/path?query#fragment
```

- **Autocorrection du navigateur** — Chrome interprète `https:attacker.com` ou `https:\/\/attacker.com` comme un lien valide vers `attacker.com`, ce qui contourne un filtre bête cherchant la chaîne exacte `https://`. Les navigateurs corrigent aussi `\` en `/`, ce qui peut transformer un `userinfo` inoffensif en hostname malveillant
- **Failles de logique de validation (allowlist "starts with / ends with")** :
    
    ```
    https://example.com/login?redir=http://example.com.attacker.com   (sous-domaine piège)https://example.com/login?redir=https://example.com@attaquant.com/example.com  (both start+end satisfaits)
    ```
    
    → Le `@` fait passer tout ce qui précède comme `userinfo`, et ce qui suit devient le vrai hostname
- **Data URLs** — encoder un script de redirection en base64 dans une `data:` URL pour passer sous un blocklist textuel
- **Double/triple encodage URL** — encoder `%2f` en `%252f`, `%25252f` pour exploiter un décalage entre la façon dont le validateur et le navigateur décodent l'URL
- **Caractères non-ASCII / homoglyphes** — un caractère Unicode visuellement proche d'un `/` (ex: `╱`, ou des caractères cyrilliques ressemblant à du latin) peut tromper un validateur qui ne normalise pas comme le navigateur

### Escalade via open redirect

- **Phishing crédible** — l'URL affichée commence par le vrai domaine, rassure la victime, puis redirige vers une fausse page de login
- **Contournement d'allowlist** — un service qui n'autorise les requêtes sortantes (ex: protection SSRF) que vers des domaines de confiance peut être trompé si l'un de ces domaines de confiance contient lui-même un open redirect
- **Vol de token via `Referer`** — si l'URL d'origine contient un token sensible en paramètre, et qu'elle redirige vers un site externe, le `Referer` transmis peut exfiltrer ce token (approfondi au chapitre 20 sur le SSO)

## Liens

- [Bug Bounty Bootcamp](../01_The_Industry/00_General.md)
- [Chapitre 6](06_Cross_Site_Scripting_%28XSS%29.md)
- Server-Side Request Forgery (SSRF)
- Bypass de protection open redirect
