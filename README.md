# Docker pour Linux — Mise en œuvre et déploiement de conteneurs virtuels

Support pédagogique complet d'une formation de trois jours (18 heures) consacrée à la mise en œuvre et au déploiement de conteneurs Docker sous Linux. Le cours s'adresse à des administrateurs systèmes Linux et à des ingénieurs DevOps qui souhaitent intégrer Docker à leurs chaînes d'intégration et de déploiement continus. Il associe un développement théorique rigoureux, des commandes commentées et reproductibles, et des laboratoires pratiques corrigés étape par étape.

## Informations

| Élément | Valeur |
|---------|--------|
| Durée totale | 18 h (3 jours de 6 h) |
| Environnement | 100 % Linux — Ubuntu 22.04 LTS ou 24.04 LTS, Docker Engine et Docker Compose v2 |
| Public visé | Administrateurs systèmes Linux, ingénieurs DevOps, administrateurs d'infrastructure |
| Prérequis | Maîtrise de l'administration Linux, du shell et des commandes de base ; notions de réseau (adressage IP, ports, TCP/IP) ; culture de la virtualisation |
| Modules | M1 à M6, répartis sur six séquences (S1 à S6) |
| Supports | Trois fichiers Markdown, un par jour, exécutables sur un hôte de laboratoire isolé |

## Planning de la formation

| Jour | Séquence | Module | Thème | Durée | Support |
|------|----------|--------|-------|-------|---------|
| Jour 1 | S1 | M1 | Introduction à Docker et aux conteneurs, installation et configuration sur Ubuntu | 3 h | [jour-1.md](jour-1.md) |
| Jour 1 | S2 | M2 | Création de conteneurs Docker, gestion des images et personnalisation d'environnements | 3 h | [jour-1.md](jour-1.md) |
| Jour 2 | S3 | M3 | Gestion avancée des conteneurs : volumes, réseaux, sauvegarde et Docker Compose | 3 h | [jour-2.md](jour-2.md) |
| Jour 2 | S4 | M4 | Surveillance des conteneurs, mise à jour, nettoyage et diagnostic | 3 h | [jour-2.md](jour-2.md) |
| Jour 3 | S5 | M5 | Sécurité et bonnes pratiques : durcissement et audit de la configuration | 3 h | [jour-3.md](jour-3.md) |
| Jour 3 | S6 | M6 | Étude de cas : déploiement complet d'une stack et intégration CI/CD | 3 h | [jour-3.md](jour-3.md) |

Chaque journée se déroule de 9h00 à 12h00, puis de 13h00 à 16h00, la pause déjeuner de 12h00 à 13h00 constituant la seule interruption. Le minutage détaillé de chaque séquence figure dans le tableau de planning en tête de chaque fichier `jour-X.md`. La progression est délibérément continue : le premier jour installe et met en œuvre, le deuxième jour approfondit la gestion courante, le troisième jour durcit et industrialise.

## Contenu du dépôt

```text
.
├── README.md
├── .gitignore
├── jour-1.md
├── jour-2.md
└── jour-3.md
```

Chaque fichier `jour-X.md` est un support autonome et complet, structuré de manière identique : objectifs du jour, planning, contenu théorique par séquence, table de correspondance avec les références de sécurité, laboratoires pratiques, corrigés détaillés, mémos de commandes essentielles et ressources complémentaires.

## Prérequis et installation

La formation se déroule intégralement sous Linux. Ubuntu 22.04 LTS ou 24.04 LTS est l'environnement de référence. L'installation de Docker s'effectue depuis le dépôt officiel de Docker, et non depuis les dépôts de la distribution, dont les paquets sont en retard sur les versions publiées.

```bash
# Mise à jour du système et installation des dépendances
sudo apt update && sudo apt upgrade -y
sudo apt install -y ca-certificates curl gnupg
# ca-certificates : autorité de certification requise pour les connexions HTTPS
# curl : transfert de fichiers depuis une URL
# gnupg : gestion des clés de chiffrement

# Création du répertoire de stockage des clés GPG
sudo install -m 0755 -d /etc/apt/keyrings
# install -m 0755 : crée le répertoire avec les permissions rwxr-xr-x
# -d : crée le répertoire s'il n'existe pas encore

# Dépôt de la clé GPG officielle de Docker, convertie au format binaire
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
# -f : n'affiche rien en cas d'échec, -s : silencieux, -S : affiche les erreurs
# -L : suit les redirections HTTP
# gpg --dearmor : convertit la clé ASCII en format binaire

# Attribution des droits de lecture à la clé
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Ajout du dépôt officiel de Docker pour la distribution Ubuntu
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

```bash
# Installation de Docker Engine et de ses extensions
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
# docker-ce : moteur Docker (Docker Community Edition)
# docker-ce-cli : interface en ligne de commande
# containerd.io : moteur d'exécution des conteneurs
# docker-buildx-plugin : extension de construction d'images
# docker-compose-plugin : Docker Compose v2, intégré au moteur

# Ajout de l'utilisateur courant au groupe docker
sudo usermod -aG docker $USER
# -a : ajoute sans remplacer les groupes existants
# -G : groupe cible ; l'appartenance à ce groupe équivaut à disposer des
#      privilèges root sur l'hôte : à n'accorder qu'à un compte de confiance

# Prise en compte immédiate du nouveau groupe, ou reconnexion de la session
newgrp docker

# Vérification de l'installation
docker --version
docker compose version

# Exécution d'un premier conteneur de vérification
docker run hello-world
# Sortie attendue : « Hello from Docker! This message shows that your
# installation appears to be working correctly. »

# Vérification du service et de l'espace disque consommé
sudo systemctl status docker
docker system df
```

## Comment utiliser ce support

- **Des fichiers autonomes.** Chaque `jour-X.md` se suffit à lui-même : le cours peut être suivi dans l'ordre chronologique, ou chapitre par chapitre pour une remise à niveau ciblée. Aucune dépendance n'existe entre les fichiers.
- **Des laboratoires enchaînés.** Chaque lab est une suite d'exercices numérotés qui s'enchaînent : le volume créé à l'exercice précédent alimente la démonstration du suivant, et la stack construite par le lab précédent sert de point de départ au suivant. L'ordre indiqué fait partie de la démarche pédagogique : il doit être respecté, car chaque exercice consomme le livrable produit par le précédent.
- **Des solutions repliées.** Les corrigés détaillés sont dissimulés dans des blocs `<details>` afin de permettre une tentative autonome avant consultation. Ouvrez-les seulement après avoir réalisé l'exercice, puis comparez votre résultat à la sortie attendue.
- **Des commandes commentées.** Chaque commande est accompagnée d'un commentaire expliquant l'option employée et, le cas échéant, la raison pédagogique du choix. Le code est destiné à être exécuté, non seulement lu.
- **Des points de contrôle.** Chaque exercice comporte des cases à cocher à valider au fur et à mesure, qui servent de grille d'auto-évaluation en fin de séquence.
- **Un laboratoire isolé.** Toutes les commandes de création de réseaux, de volumes, de suppression et de nettoyage (pruning) sont conçues pour un hôte de laboratoire dédié. Avant de les exécuter, lisez attentivement la section « Avertissement » ci-dessous.

## Fil conducteur

La formation suit un fil conducteur unique, annoncé dès le premier jour et développé jusqu'au troisième : la *kill chain* des conteneurs, c'est-à-dire l'ensemble des étapes par lesquelles un conteneur est créé, configuré, exécuté, puis éventuellement exploité par un attaquant. Chaque notion technique est replacée dans cette chaîne afin de toujours répondre à une seule question : comment ce mécanisme peut-il servir une attaque, et quelle contre-mesure le neutralise-t-on ?

Deux référentiels de référence encadrent cette démarche et sont cités explicitement tout au long du support :

| Référentiel | Rôle dans la formation |
|-------------|------------------------|
| [CIS Docker Benchmark](https://www.cisecurity.org/benchmark/docker) | Référentiel de configuration de référence, cité par identifiant de contrôle, pour l'audit et le durcissement de l'hôte et des conteneurs |
| [MITRE ATT&CK — Container Techniques](https://attack.mitre.org/matrices/enterprise/containers/) | Matrice d'attaques propre aux conteneurs, citée par identifiant de technique, pour qualifier les menaces et justifier les contre-mesures |

L'objectif final du parcours est le déploiement sécurisé : une image construite, analysée, durcie, publiée dans un registry maîtrisé, déployée par un pipeline d'intégration et de déploiement continus, et enfin auditée.

## Avertissement

- Les commandes de ce support sont exécutables sur un **hôte Linux de laboratoire isolé**. Plusieurs d'entre elles suppriment des ressources de façon irréversible : `docker system prune --volumes`, la suppression de volumes nommés, le montage d'un système de fichiers en lecture seule, la modification du groupe `docker` et l'exposition du socket Docker. Elles ne doivent pas être reconduites telles quelles sur un système de production.
- L'appartenance au groupe `docker` confère des privilèges équivalents à ceux de root sur l'hôte, puisqu'elle permet de démarrer un conteneur privilégié. N'accordez ce groupe qu'à des comptes de confiance, et preferrez l'exécution en mode rootless lorsque le contexte le permet.
- Les adresses de registries employées dans les exemples sont **fictives** : `registry.demo.example`, `proxy.demo.example` et `ghcr.io/demo-org/…` n'existent pas. Remplacez-les par les noms de votre propre organisation et par les adresses de vos registries réels avant toute exploitation.
- Les identifiants d'illustration (`demo`, `demo_app`, les mots de passe de laboratoire) n'ont aucune valeur de secret et ne doivent pas être réutilisés tels quels.

## Licence

Ce contenu est mis à disposition à titre pédagogique, en l'état, pour un usage de formation et d'auto-formation. Le choix d'une licence ouverte — par exemple MIT, ou une licence adaptée à un support de formation — reste à confirmer par le mainteneur du dépôt. Aucun fichier de licence n'est fourni pour l'instant : le contenu est considéré comme mis à disposition sans garantie, jusqu'à ce qu'une licence soit explicitement indiquée.

## Ressources complémentaires

| Document | Lien |
|----------|------|
| Documentation officielle Docker | https://docs.docker.com/ |
| Documentation Docker Compose v2 | https://docs.docker.com/compose/ |
| CIS Docker Benchmark | https://www.cisecurity.org/benchmark/docker |
| MITRE ATT&CK — Container Techniques | https://attack.mitre.org/matrices/enterprise/containers/ |
| Docker Scout | https://docs.docker.com/scout/ |
| Trivy — analyse de vulnérabilités | https://trivy.dev/ |
