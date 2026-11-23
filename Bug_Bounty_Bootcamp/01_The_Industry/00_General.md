# Bug Bounty Bootcamp

> **Source :** Bug Bounty Bootcamp — Vickie Li · **Type :** note-maîtresse  
> `#bug-bounty` `#pentesting` `#cybersécurité` `#livre` `#web-hacking`

## Résumé général

Guide pratique pour se lancer et progresser dans le bug bounty (chasse aux vulnérabilités rémunérée). Le livre couvre quatre grandes parties : l'industrie du bug bounty, les bases techniques (fonctionnement d'internet, mise en place d'un environnement de test), les vulnérabilités web une par une (XSS, IDOR, SQLi, SSRF, etc.), et des techniques avancées (revue de code, hacking mobile, API, fuzzing).

## Structure du livre

### Partie I — L'industrie (p.26)

- [Chapitre 1 : Choisir un programme](01_Bounty_Programs.md)
- [Chapitre 2 : Pérenniser son succès](02_Reporting.md) (rédaction de rapports, relation avec les équipes de dev)

### Partie II — Les bases (p.56)

- [Chapitre 3 : Fonctionnement d'internet](../02_Basics/03_Internet.md) (modèle client-serveur, DNS, HTTP, JWT, same-origin policy)
- [Chapitre 4 : Environnement de test et interception de trafic](../02_Basics/04_Lab_Environment.md) (Burp Suite)
- [Chapitre 5 : Reconnaissance web](../02_Basics/05_Recon.md) (OSINT, énumération de sous-domaines, Google dorking)

### Partie III — Vulnérabilités web (p.134) — le cœur du livre

- Ch.6 [Cross-Site Scripting (XSS)](../03_Web_Vulnerabilities/06_Cross_Site_Scripting_%28XSS%29.md)
- Ch.7 [Open Redirects](../03_Web_Vulnerabilities/07_Open_Redirects.md)
- Ch.8 [Clickjacking](../03_Web_Vulnerabilities/08_Clickjacking.md)
- Ch.9 [Cross-Site Request Forgery (CSRF)](../03_Web_Vulnerabilities/09_Cross_Site_Request_Forgery_%28CSRF%29.md)
- Ch.10 [Insecure Direct Object Reference (IDOR)](../03_Web_Vulnerabilities/10_Insecure_Direct_Object_References_%28IDOR%29.md)
- Ch.11 [Injection SQL](../03_Web_Vulnerabilities/11_SQL_Injection.md)
- Ch.12 [Race Conditions](../03_Web_Vulnerabilities/12_Race_Conditions.md)
- Ch.13 Server-Side Request Forgery (SSRF)
- Ch.14 Désérialisation non sécurisée
- Ch.15 XML External Entity (XXE)
- Ch.16 Injection de template
- Ch.17 Erreurs de logique applicative / contrôle d'accès défaillant
- Ch.18 Exécution de code à distance (RCE)
- Ch.19 Vulnérabilités liées à la Same-Origin Policy
- Ch.20 Failles de Single Sign-On (SSO)
- Ch.21 Divulgation d'information

### Partie IV — Techniques expertes (p.358)

- Ch.22 Revue de code
- Ch.23 Hacking d'applications Android
- Ch.24 Hacking d'API — lien avec Hacking APIs
- Ch.25 Découverte automatique de vulnérabilités (fuzzing)

## Liens transversaux

- Cybersécurité
- Pentest
- OWASP Top 10
- Hacking APIs
- Reconnaissance (OSINT)
