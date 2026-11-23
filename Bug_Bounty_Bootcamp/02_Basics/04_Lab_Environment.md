[⬅ Retour à Bug Bounty Bootcamp](../01_The_Industry/00_General.md)

# Chapitre 4 — Environnement de test et interception de trafic

> **Source :** Bug Bounty Bootcamp — Chapitre 4 · **Type :** note-chapitre  
> `#bug-bounty` `#cybersécurité` `#burp-suite` `#outils`

## Résumé

Mise en place du labo de hacking web : choix d'OS, navigateur + proxy, installation du certificat CA pour intercepter le HTTPS, puis tour d'horizon des modules principaux de Burp Suite (Proxy, Intruder, Repeater, Decoder, Comparer). Se termine par des conseils de prise de notes — le livre recommande d'ailleurs explicitement Obsidian.

## Concepts clés

### Choix de l'OS

- Recommandé : système Unix-like (Kali Linux ou macOS) → la majorité des outils de hacking open source y sont conçus nativement
- Kali Linux embarque déjà Burp Suite, DirBuster/Gobuster (recon), Wfuzz (fuzzing)

### Proxy web

- Se place entre le navigateur et le serveur : `Browser <--> Proxy <--> Serveur`
- Permet de voir, intercepter et modifier chaque requête/réponse avant qu'elle n'atteigne sa destination
- Les deux proxies principaux du milieu : **Burp Suite** (Pro ou Community) et **ZAP** (gratuit)

### Configuration Burp + Firefox

1. Régler le proxy manuel de Firefox sur `127.0.0.1:8080` (Burp écoute sur ce port par défaut)
2. Installer le certificat CA de Burp (via `http://burp/`) dans Firefox pour pouvoir déchiffrer le HTTPS — sans ça, seul le trafic HTTP est visible
3. Activer "Intercept is on" dans l'onglet Proxy pour capturer chaque requête avant transfert

### Modules de Burp Suite

|Module|Usage principal|
|---|---|
|**Proxy**|Intercepter/modifier une requête en direct avant envoi|
|**Intruder**|Automatiser l'envoi de requêtes en variant un paramètre (brute-force, fuzzing)|
|**Repeater**|Modifier et renvoyer manuellement une requête pour tester différentes variantes sur un même endpoint|
|**Decoder**|Encoder/décoder du contenu (base64, hex, URL...) — "Smart decode" détecte automatiquement le type|
|**Comparer**|Comparer deux requêtes/réponses et surligner les différences|

**Exemple d'usage de l'Intruder pour du brute-force :**

```
POST /login HTTP/1.1
Host: example.com

username=alice&password=§PAYLOAD§
```

→ On définit `§PAYLOAD§` comme position à remplacer, puis on charge une liste de mots de passe courants comme "payload list".

**Copier une requête pour la documenter :** clic droit → _Copy as curl command_ (pratique pour l'inclure telle quelle dans un rapport ou une note).

### Prise de notes en bug bounty

Conseils concrets de l'auteure :

- Noter tout comportement bizarre, feature cachée, misconfiguration ou endpoint suspect — même non exploitable dans l'immédiat, ça peut servir plus tard dans un chaînage de bugs
- Garder une trace de ce qui a déjà été testé pour éviter de perdre du temps à re-tester
- Documenter chaque type de vulnérabilité appris : concept théorique, impact potentiel, étapes d'exploitation, exemple de POC
- Organiser par cible (un dossier par programme/entreprise), avec sous-dossiers par sujet
- Outils suggérés : Sublime Text (texte brut), **Obsidian** (notes en Markdown, liens entre notes), XMind (mind maps)
- Sauvegarder régulièrement (cloud ou disque externe)

> Note perso : c'est exactement l'approche suivie dans ce dépôt — une note par vulnérabilité/concept, liées entre elles.

## Liens

- [Bug Bounty Bootcamp](../01_The_Industry/00_General.md)
- [Chapitre 3](03_Internet.md)
- Burp Suite
- Burp Intruder
- Prise de notes en bug bounty
