[⬅ Retour à Bug Bounty Bootcamp](../01_The_Industry/00_General.md)

# Chapitre 12 — Race Conditions

> **Source :** Bug Bounty Bootcamp — Chapitre 12 · **Type :** note-chapitre  
> `#bug-bounty` `#cybersécurité` `#race-condition` `#web-hacking`

## Résumé

Une race condition survient quand deux portions de code censées s'exécuter en séquence s'exécutent dans le désordre à cause de la concurrence (multithreading/multiprocessing). Devient une vulnérabilité de sécurité (aussi appelée **time-of-check / time-of-use**) quand une action sensible s'exécute avant qu'une vérification de sécurité ne soit terminée. Cible privilégiée : argent, votes, crédits, cartes cadeaux.

## Concepts clés

### Mécanisme

Exécution normale attendue pour un transfert bancaire de 500$ (compte A → B, solde initial A=500$) :

1. Vérifier le solde de A (500$, suffisant)
2. Créditer B (+500$)
3. Débiter A (-500$)

Si deux requêtes de transfert identiques sont envoyées **simultanément**, le scheduler peut entrelacer les threads ainsi :

1. Thread 1 vérifie le solde de A → 500$, suffisant
2. Thread 2 vérifie le solde de A → **encore 500$** (le débit du thread 1 n'a pas encore eu lieu !) → suffisant aussi
3. Les deux threads créditent B et débitent A → Résultat : B reçoit 1000$ au lieu de 500$, de l'argent créé "à partir de rien".

Même logique applicable à un système de vote en ligne : envoyer 2 requêtes de vote simultanées peut faire passer les deux avant que le flag "a déjà voté" ne soit posé par l'une d'elles.

### Prévention (donc obstacle potentiel)

- **Verrouillage de ressource (resource lock)** — un thread verrouille la ressource pendant sa modification, forçant les autres threads à attendre
- **Principe du moindre privilège** en complément (limite l'impact si la race condition est quand même exploitée)

### Méthodologie de chasse

1. **Repérer les fonctionnalités à risque** — tout ce qui touche à un nombre : transfert bancaire, solde de carte cadeau, votes, scores de jeu, paiement e-commerce
    
2. **Envoyer des requêtes simultanées** — copier la requête (`Copy as curl command` dans Burp), puis lancer plusieurs instances en parallèle en shell :
    
    ```bash
    curl (transfert 3000$) & curl (transfert 3000$) & curl (transfert 3000$) & curl (transfert 3000$)
    ```
    
    Le `&` en fin de commande Linux exécute en arrière-plan, en parallèle
    
    ⚠️ Tester une opération qui **ne devrait être permise qu'une seule fois** — inutile de tester un montant supérieur au solde (échouerait de toute façon), ni un montant que le solde permettrait plusieurs fois normalement
    
3. **Vérifier le résultat** — si le compte destinataire reçoit plus que le montant attendu après l'envoi simultané, la race condition est confirmée. Le succès dépend en partie de la chance (algorithme d'ordonnancement du serveur) — répéter l'essai plusieurs fois si le premier échoue, et envoyer un plus grand nombre de requêtes en parallèle augmente les chances
    
4. **Documenter en PoC reproductible** — indiquer le solde de départ, la commande exacte, et le résultat observé
    

## Liens

- [Bug Bounty Bootcamp](../01_The_Industry/00_General.md)
- [Chapitre 11](11_SQL_Injection.md)
