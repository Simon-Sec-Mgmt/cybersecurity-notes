# 🛡️ Notes de cybersécurité — Veille & synthèses de lecture

Notes d'étude personnelles en **français** sur le pentest, le bug bounty et l'analyse réseau. Je les rédige au fil de mes lectures dans le cadre de ma reconversion en cybersécurité.

Chaque note suit la même structure : **résumé → concepts clés → exemples (requêtes, payloads, commandes) → notions liées**.

> Rédigées à l'origine dans [Obsidian](https://obsidian.md), puis converties en Markdown standard pour GitHub.

---

## 📚 Sommaire

### Bug Bounty Bootcamp — Vickie Li (No Starch Press)

📖 [Note maîtresse : vue d'ensemble du livre](Bug_Bounty_Bootcamp/01_The_Industry/00_General.md)

**Partie I — L'industrie**
| # | Chapitre |
|---|---|
| 1 | [Choisir un programme de bug bounty](Bug_Bounty_Bootcamp/01_The_Industry/01_Bounty_Programs.md) |
| 2 | [Pérenniser son succès (rédaction de rapports)](Bug_Bounty_Bootcamp/01_The_Industry/02_Reporting.md) |

**Partie II — Les bases**
| # | Chapitre |
|---|---|
| 3 | [Comment fonctionne internet (HTTP, DNS, JWT, SOP)](Bug_Bounty_Bootcamp/02_Basics/03_Internet.md) |
| 4 | [Environnement de test & Burp Suite](Bug_Bounty_Bootcamp/02_Basics/04_Lab_Environment.md) |
| 5 | [Reconnaissance web](Bug_Bounty_Bootcamp/02_Basics/05_Recon.md) |

**Partie III — Vulnérabilités web**
| # | Chapitre |
|---|---|
| 6 | [Cross-Site Scripting (XSS)](Bug_Bounty_Bootcamp/03_Web_Vulnerabilities/06_Cross_Site_Scripting_%28XSS%29.md) |
| 7 | [Open Redirects](Bug_Bounty_Bootcamp/03_Web_Vulnerabilities/07_Open_Redirects.md) |
| 8 | [Clickjacking](Bug_Bounty_Bootcamp/03_Web_Vulnerabilities/08_Clickjacking.md) |
| 9 | [Cross-Site Request Forgery (CSRF)](Bug_Bounty_Bootcamp/03_Web_Vulnerabilities/09_Cross_Site_Request_Forgery_%28CSRF%29.md) |
| 10 | [Insecure Direct Object References (IDOR)](Bug_Bounty_Bootcamp/03_Web_Vulnerabilities/10_Insecure_Direct_Object_References_%28IDOR%29.md) |
| 11 | [Injection SQL](Bug_Bounty_Bootcamp/03_Web_Vulnerabilities/11_SQL_Injection.md) |
| 12 | [Race Conditions](Bug_Bounty_Bootcamp/03_Web_Vulnerabilities/12_Race_Conditions.md) |

*En cours : chapitres 13 à 25.*

### Outils

| Outil | Note |
|---|---|
| Wireshark | [Filtres de capture & d'affichage](Wireshark/00_Concepts.md) |

---

## 📖 Sources

- **Bug Bounty Bootcamp: The Guide to Finding and Reporting Web Vulnerabilities** — Vickie Li, No Starch Press, 2021. → [nostarch.com/bug-bounty-bootcamp](https://nostarch.com/bug-bounty-bootcamp)
- **Wireshark Cheat Sheet** — Hack Academy

Ces notes sont des **synthèses personnelles reformulées**. Elles ne remplacent pas les ouvrages originaux, et je recommande vivement de les acheter : tout le mérite du contenu revient à leurs auteurs.

## ⚠️ Avertissement

Les techniques décrites ici ont un **but éducatif** et ne doivent servir que dans un cadre **légal et autorisé** : labs personnels, plateformes d'entraînement (PortSwigger Web Security Academy, HackTheBox, TryHackMe…) ou programmes de bug bounty, en respectant strictement leur scope. Tester un système sans autorisation explicite est illégal.

## 📄 Licence

Notes sous licence [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.fr) : tu peux les partager et les adapter, à condition de citer la source, sans usage commercial et sous la même licence.
