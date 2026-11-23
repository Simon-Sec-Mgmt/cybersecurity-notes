# Wireshark Cheat Sheet

> **Source :** Wireshark Cheat Sheet — Hack Academy · **Type :** fiche-outil  
> `#réseau` `#wireshark` `#analyse-trafic` `#cybersécurité` `#pentesting` `#cheat-sheet`

## Résumé

Fiche de référence rapide pour Wireshark, l'analyseur de protocoles réseau. Distingue deux familles de filtres : les **filtres de capture** (appliqués avant l'enregistrement du trafic, syntaxe BPF) et les **filtres d'affichage** (appliqués après, sur les paquets déjà capturés, plus flexibles). Se termine par un rappel éthique/légal sur l'usage de l'outil.

## Concepts clés

### Filtre de capture

- S'applique **avant** le début de la capture
- Réduit le volume de données enregistrées
- Syntaxe type BPF (Berkeley Packet Filter), moins flexible que les filtres d'affichage
- Exemples utiles :
    - `host 172.18.5.4` — trafic vers/depuis une IP précise
    - `net 192.168.0.0/24` — trafic vers/depuis une plage d'IP
    - `port 53` — uniquement le trafic DNS
    - `ip` — uniquement IPv4
    - `not broadcast and not multicast` — trafic unicast seulement

### Filtre d'affichage

- S'applique **après** la capture, sur les paquets déjà collectés
- Beaucoup plus flexible (accès aux champs de protocole)
- Exemples utiles :
    - `tcp.port eq 25 or icmp` — SMTP + ICMP
    - `tcp.window_size == 0 && tcp.flags.reset != 1` — buffer TCP plein, la source demande l'arrêt
    - `smb || nbns || dcerpc || nbss || dns` — trafic lié à Windows
    - `eth.addr[0:3] == 00:06:5B` — filtrage par constructeur MAC (ici Dell)
    - `http.request.uri matches "gl=se$"` — expression régulière sur une URI

### Analyse réseau

- Wireshark sert autant au dépannage réseau qu'à l'analyse de sécurité (détection de menaces)
- La pratique régulière est nécessaire pour maîtriser les filtres avancés

### Éthique et légalité

- Toujours obtenir une **autorisation explicite** avant de capturer du trafic réseau
- Un usage non autorisé peut constituer une violation de vie privée et avoir des conséquences légales
- Lié à la notion de Pentest — rejoint les principes d'engagement autorisé (scope, rules of engagement)

## Liens

- Wireshark
- Filtre de capture
- Filtre d'affichage
- Analyse réseau
- Pentest
- Cybersécurité

## À retenir

> Filtres de capture = avant, pour réduire le volume. Filtres d'affichage = après, pour analyser en détail.
