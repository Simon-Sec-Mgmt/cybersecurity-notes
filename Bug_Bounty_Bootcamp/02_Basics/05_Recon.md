[⬅ Retour à Bug Bounty Bootcamp](../01_The_Industry/00_General.md)

# Chapitre 5 — Reconnaissance web

> **Source :** Bug Bounty Bootcamp — Chapitre 5 · **Type :** note-chapitre  
> `#bug-bounty` `#cybersécurité` `#reconnaissance` `#osint` `#recon`

## Résumé

La reconnaissance sert à cartographier la surface d'attaque avant de chercher des bugs : quels domaines, sous-domaines, services, technologies sont utilisés. Chapitre long, orienté outils concrets (WHOIS, énumération de sous-domaines, scan de ports, brute-force de répertoires, S3, GitHub) puis automatisation en bash.

## Concepts clés

### Parcours manuel de la cible

- Explorer chaque fonctionnalité de l'application (y compris les moins utilisées), créer des comptes à différents niveaux de privilège
- Objectif : comprendre l'attack surface avant de scanner quoi que ce soit

### Google Dorking

Opérateurs de recherche avancée utiles en recon :

|Opérateur|Usage|
|---|---|
|`site:`|Limiter à un domaine|
|`inurl:`|Chercher un motif dans l'URL (ex : `inurl:"/admin/config.php"`)|
|`intitle:`|Chercher dans le titre de page (ex : `intitle:"index of"` → listing de répertoire)|
|`filetype:` / `ext:`|Filtrer par extension (`.log`, `.php`, `.txt`)|
|`*`|Wildcard|
|`" "`|Correspondance exacte|
|`\|`|OU logique|
|`-`|Exclusion|

Exemple combiné : `site:example.com ext:txt password` pour trouver des fichiers texte exposant potentiellement des mots de passe. → Existence de la **Google Hacking Database** (exploit-db.com) qui référence des dorks prêts à l'emploi. ⚠️ Trop de requêtes = CAPTCHA de Google — éviter sur un réseau partagé/corpo.

### Découverte de scope

Toujours vérifier le scope officiel du programme avant de commencer.

- **WHOIS / reverse WHOIS** : `whois example.com` révèle les infos d'enregistrement ; la recherche inverse (via un outil comme ViewDNS.info) permet de retrouver tous les domaines liés à un même propriétaire
- **IP et ASN** : `nslookup` pour trouver l'IP, puis `whois <IP>` pour voir si l'organisation possède une plage IP dédiée (champ `NetRange`). Comparer les ASN (`whois -h whois.cymru.com <IP>`) de plusieurs IP pour confirmer qu'elles appartiennent au même propriétaire
- **Parsing de certificats SSL** : le champ _Subject Alternative Name_ d'un certificat liste souvent d'autres sous-domaines de la même organisation → outils : crt.sh, Censys, Cert Spotter
- **Énumération de sous-domaines** : outils Sublist3r (moteurs de recherche), SubBrute (brute-force), Amass (combine plusieurs sources), Gobuster (`gobuster dns -d domaine -w wordlist`). Wordlists de référence : SecLists (Daniel Miessler)
    - Astuce : deviner des sous-domaines par permutation (`1.example.com` existe → tester `2.example.com`) avec un outil comme Altdns
    - Chercher aussi les sous-domaines de sous-domaines (récursif)
- **Énumération de services** :
    - Scan actif (Nmap, Masscan) → contact direct avec la cible : `nmap cible.com`
    - Scan passif (Shodan, Censys) → aucune interaction directe, plus discret
- **Brute-force de répertoires** : Dirsearch ou Gobuster testent des chemins à partir d'une wordlist ; code 200 = existe, 403 = existe mais protégé (à examiner de près pour un bypass), 404 = n'existe pas
    
    ```
    gobuster dir -u https://cible.com -w wordlist.txt
    ```
    
- **Spidering** : crawl récursif du site pour découvrir tous les liens/endpoints (ZAP Spider ou le Crawler de Burp)
- **Hébergement tiers (S3)** : chercher les buckets Amazon S3 exposés via Google dorking (`site:s3.amazonaws.com NOM_ENTREPRISE`), GrayhatWarfare, ou brute-force (Lazys3, Bucket Stream) — un bucket mal configuré peut exposer logs, credentials, code source
- **GitHub Recon** : chercher des secrets/clés API oubliées dans les dépôts publics liés à l'organisation
- **Fingerprinting de stack technique** : en-têtes HTTP, extensions de fichiers (ex: un chemin contenant `phpmyadmin` trahit une stack PHP), outils comme Wappalyzer

### Automatisation (bash)

Le reste du chapitre montre comment écrire ses propres scripts de recon : bases du bash scripting, sauvegarde des résultats dans des fichiers datés, options pour choisir quels outils lancer, parsing des résultats, construction d'un rapport maître combinant plusieurs outils, scan de plusieurs domaines, et programmation de scans automatiques (cron). L'idée centrale : combiner les sorties de plusieurs outils de recon en un seul pipeline reproductible plutôt que de tout lancer manuellement à chaque fois.

## Liens

- [Bug Bounty Bootcamp](../01_The_Industry/00_General.md)
- [Chapitre 4](04_Lab_Environment.md)
- Énumération de sous-domaines
- Google Dorking
- GitHub Recon
- Surface d'attaque
