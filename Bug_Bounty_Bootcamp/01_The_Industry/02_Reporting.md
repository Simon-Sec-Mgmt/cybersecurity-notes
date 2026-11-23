[⬅ Retour à Bug Bounty Bootcamp](00_General.md)

# Chapitre 2 — Pérenniser son succès

> **Source :** Bug Bounty Bootcamp — Chapitre 2 · **Type :** note-chapitre  
> `#bug-bounty` `#cybersécurité` `#pentesting` `#rédaction-rapport`

## Résumé

Trouver une faille n'est que la moitié du travail : il faut aussi bien la communiquer. Ce chapitre détaille l'anatomie d'un bon rapport de vulnérabilité, la gestion de la relation avec les équipes de dev, les raisons typiques d'échec des débutants, et comment sortir d'une période sans trouvaille.

## Concepts clés

### Anatomie d'un rapport de bug bounty

Un bon rapport suit 8 étapes :

1. **Titre descriptif** — précis, avec type de faille + endpoint + impact
    
    - Mauvais : _"IDOR on a Critical Endpoint"_
    - Bon : _"IDOR sur /change_password menant à une prise de compte totale"_
2. **Résumé clair** — décrit techniquement le bug. Exemple structurel :
    
    ```
    Endpoint : POST /change_password
    Paramètres : user_id, new_password
    Problème : user_id n'est pas validé côté serveur → 
               un attaquant peut changer le mot de passe de n'importe qui
    ```
    
3. **Évaluation de sévérité** — échelle à 4 niveaux :
    
    |Niveau|Exemple|
    |---|---|
    |Low|Open redirect exploitable seulement pour du phishing|
    |Medium|CSRF sur une action sensible (ex: changement de mdp)|
    |High|Open redirect utilisé pour voler des tokens OAuth|
    |Critical|SQLi menant à du RCE en production|
    
    → Se référer au CVSS (Common Vulnerability Scoring System) pour objectiver la note, et adapter selon les priorités métier du client (une fuite de date de naissance n'a pas le même poids sur un site de rencontre que sur un site de recherche d'emploi).
    
4. **Étapes de reproduction** — détaillées, sans supposer que le lecteur connaît l'appli. Exemple de bonne pratique :
    
    ```
    1. Créer deux comptes : A et B
    2. Se connecter en tant que A, aller sur /change_password
    3. Intercepter la requête POST, remplacer user_id par celui de B
    4. Se connecter à B avec le nouveau mot de passe
    ```
    
5. **Preuve de concept (POC)** — fichier HTML pour un CSRF, XML pour un XXE, vidéo pour un enchaînement complexe. Le but : que l'équipe sécurité n'ait rien à recréer elle-même.
    
6. **Impact et scénario d'attaque** — différent de la sévérité : décrit concrètement ce qu'un attaquant ferait (ex : énumérer tous les `user_id` séquentiels pour prendre en masse des comptes).
    
7. **Recommandation de mitigation** — rester général (ex: "valider que `user_id` correspond à l'utilisateur authentifié") sans prétendre connaître le code interne.
    
8. **Validation finale** — rejouer soi-même les étapes avant soumission.
    

### Pourquoi les rapports sont rejetés

- **N/A** : hors scope → toujours relire la politique du programme avant de tester
- **Informative** : le bug est valide mais l'entreprise s'en moque → se mettre à la place de l'équipe sécurité, comprendre ses priorités business
- **Duplicate** : inévitable en partie, mais réduit en visant des programmes à large scope, en faisant du recon poussé, et en visant les Programmes privés
- **Astuce clé : chaîner les bugs** — un bug mineur (ex: open redirect) rejeté seul peut devenir critique combiné à un autre (ex: intégré dans une SSRF)

### Erreurs fréquentes des débutants

- Changer de programme trop vite au lieu de creuser en profondeur ou d'élargir méthodiquement
- Sauter le recon et foncer directement sur les cibles évidentes
- Se limiter aux scanners automatiques / bugs évidents déjà trouvés par d'autres
- Éviter les programmes privés, pourtant bien moins compétitifs

### Que faire en cas de blocage

1. Faire une pause (la fatigue mentale nuit à la créativité)
2. Monter en compétence : guides OWASP (Web Security Testing Guide, Mobile Security Testing Guide), CTF pour découvrir de nouvelles classes de vulnérabilités
3. Diversifier les cibles, chercher des comportements "bizarres" plutôt que des vulnérabilités précises, dans l'idée de les chaîner ensuite

## Liens

- [Bug Bounty Bootcamp](00_General.md)
- [Chapitre 1](01_Bounty_Programs.md)
- CVSS
- OWASP
- Server-Side Request Forgery (SSRF)
- Programmes privés
