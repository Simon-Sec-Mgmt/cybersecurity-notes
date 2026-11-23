[⬅ Retour à Bug Bounty Bootcamp](00_General.md)

# Chapitre 1 — Choisir un programme de bug bounty

> **Source :** Bug Bounty Bootcamp — Chapitre 1 · **Type :** note-chapitre  
> `#bug-bounty` `#cybersécurité` `#pentesting`

## Résumé

Un programme de bug bounty est une initiative par laquelle une entreprise invite des hackers à tester ses produits en échange d'une récompense. Le chapitre explique comment évaluer et choisir un programme selon le type d'actif ciblé, la plateforme utilisée, le scope, les montants versés et le temps de réponse.

## Concepts clés

### Historique rapide

- Netscape (1995) : premier programme de bug bounty de l'histoire
- Mozilla (2004), puis Google et Facebook (2010-2011) démocratisent la pratique
- Apparition de plateformes intermédiaires comme HackerOne et Bugcrowd (2012)

### Types d'actifs en bug bounty

- **Sites/applications sociales** — riches en IDOR, fuites d'info, prise de compte ; bon point de départ pour débutant
- **Applications web générales** — moins de surface d'attaque orientée utilisateur, plus orientée bugs côté serveur
- **Applications mobiles** — nécessite un labo (device rooté + émulateurs), compétences en reverse engineering
- **API** — fuites de données, failles d'injection
- **Code source / exécutables** — demande des compétences avancées en reverse engineering, moins compétitif
- **Hardware / IoT** — très spécialisé (signature numérique, chiffrement asymétrique), le moins compétitif

### Plateformes de bug bounty vs programmes indépendants

- Plateformes (HackerOne, Bugcrowd, Synack, Cobalt, Intigriti) : transparence, gestion logistique, système de réputation, résolution de conflits
- Inconvénient : triage par des tiers parfois peu familiers du produit, programmes publics souvent saturés
- Programmes indépendants (Google, Apple, Facebook) : lien direct avec les équipes de sécurité, mais moins de transparence

### Scope (bug bounty)

- **Scope d'actifs** : quels domaines/produits sont autorisés à être testés
- **Scope de vulnérabilités** : quels types de failles sont acceptés comme rapports valides
- Sortir du scope = illégal — règle absolue à respecter

### Rémunération

- **VDP (Vulnerability Disclosure Program)** : pas de paiement, uniquement réputation — bon pour l'apprentissage, moins compétitif
- **Programmes payants** : rémunération proportionnelle à la sévérité (de ~50$ à plus de 10 000$, jusqu'à 1M$ chez Apple pour les failles critiques)

### Temps de réponse

- Un programme réactif accélère l'apprentissage (feedback rapide sur la qualité des rapports)
- Les délais viennent souvent d'un manque de ressources côté équipe sécurité

### Programmes publics vs privés

- Privés = accès sur invitation, moins de concurrence, réponses plus rapides
- Publics = ouverts à tous, plus compétitifs

## Liens

- [Bug Bounty Bootcamp](00_General.md)
- HackerOne
- Bugcrowd
- Scope (bug bounty)
- Types d'actifs en bug bounty
