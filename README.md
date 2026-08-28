# RIA — canal de diffusion beta

Ce dépôt sert le **canal beta** de RIA : les manifestes de release et les octets que les
installations vont chercher pour se mettre à jour.

Il est public, et c'est délibéré.

## Pourquoi un dépôt public ne pose pas de problème

La racine de confiance n'est pas le transport, c'est la **signature**.

Chaque manifeste est signé en Ed25519 avec une clé dont la partie privée n'est jamais sur
le serveur qui sert les fichiers, et dont la partie publique est gravée dans le binaire
qui vérifie. Un nœud refuse tout manifeste qu'il ne peut pas authentifier, quelle que
soit sa provenance.

En conséquence directe :

- **aucune identité n'est nécessaire pour lire ce canal** — pas de secret machine, pas de
  jeton, pas d'activation RIA Network. Une installation locale doit pouvoir être corrigée
  sans avoir à rejoindre quoi que ce soit ;
- **aucune requête ne porte d'identité** — un canal qui en demanderait une rendrait le
  parc lisible par qui sert les fichiers ;
- **remplacer un fichier ici ne suffit pas à faire installer quoi que ce soit.** Les
  empreintes vivent dans le manifeste signé : des octets substitués sont refusés, et la
  machine continue de tourner sur ce qu'elle avait.

Ce dépôt ne contient donc, et ne doit jamais contenir, **aucune clé privée ni aucun
secret**.

## Disposition

    beta/current.json                ce que les machines lisent
    beta/releases/<release-id>.json  chaque manifeste publié, archivé
    beta/artifacts/<fichier>         les octets
    beta/installateurs/<fichier>.pkg les installateurs, pour une première installation

## Une release, deux architectures

Un manifeste couvre `darwin/arm64` **et** `darwin/amd64`. Chaque machine y trouve la
sienne, et seulement la sienne.

La publication refuse une couverture inégale : un composant livré pour une architecture et
oublié pour l'autre est arrêté à la source, plutôt que découvert sur la machine qui le
subirait.

## Vérifier ce canal soi-même

    ria-release verifier --canal beta --racine . --cle-publique <clé publique de release>

Le contrôle porte sur les octets **servis**, pas sur ceux annoncés : le manifeste peut être
parfait et un fichier avoir été remplacé après coup.

## Ce que ce dépôt n'est pas

Ce n'est pas le code de RIA. Il ne contient que ce qui est diffusé.
