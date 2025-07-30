**MODULE 1 – INTRODUCTION À DOCKER ET À LA CONTENEURISATION**

---

### 1. Objectifs pédagogiques

À l’issue de ce module, l’apprenant sera capable de :

* Expliquer les différences entre virtualisation et conteneurisation.
* Décrire l’architecture technique de Docker.
* Identifier les cas d’usage de Docker en environnement de développement ou de production.
* Installer et tester Docker sur son système local.

---

### 2. Virtualisation vs Conteneurisation

**2.1. Définition de la virtualisation :**

* Technique qui consiste à faire tourner un système d’exploitation complet à l’intérieur d’un autre.
* Chaque machine virtuelle (VM) embarque son propre OS, ses librairies, ses applications.
* Utilise un hyperviseur (ex. : VirtualBox, VMware, Hyper-V, KVM).

**Inconvénients :**

* Poids élevé (RAM, disque, CPU).
* Lenteur au démarrage.
* Redondance des OS.

**2.2. Définition de la conteneurisation :**

* Les conteneurs partagent le noyau du système hôte.
* Chaque conteneur embarque uniquement l’application et ses dépendances.
* Technologie plus légère, plus rapide, plus portable.

**Comparaison synthétique :**

| Critère            | Virtualisation (VM) | Conteneurisation (Docker)      |
| ------------------ | ------------------- | ------------------------------ |
| OS par instance    | Oui                 | Non (partage du noyau)         |
| Temps de démarrage | Secondes à minutes  | Millisecondes                  |
| Taille             | Plusieurs Go        | Quelques Mo                    |
| Isolation          | Forte               | Moyenne à forte (selon config) |
| Performance        | Moins bonne         | Meilleure                      |
| Portabilité        | Moyenne             | Excellente                     |

---

### 3. Présentation de Docker

**3.1. Historique :**

* Créé en 2013 par Solomon Hykes chez dotCloud.
* Basé sur les fonctionnalités des namespaces et cgroups de Linux.
* Docker Inc. standardise la conteneurisation moderne.

**3.2. Cas d’usage :**

* Développement local : environnements reproductibles.
* Intégration continue : tests et builds en conteneur.
* Déploiement en production : portabilité sur n’importe quel cloud.
* Microservices : isolation et gestion indépendante des services.

---

### 4. Architecture Docker

**4.1. Composants principaux :**

* **Docker Engine** : moteur installé sur le système, composé du daemon (`dockerd`).
* **Docker CLI** : interface en ligne de commande (`docker`) pour communiquer avec le daemon.
* **Registry** : dépôt d’images (DockerHub par défaut).
* **Image** : "template" en lecture seule contenant OS + application.
* **Conteneur** : instance d’exécution d’une image.
* **Dockerfile** : fichier décrivant comment construire une image.

**4.2. Fonctionnement général :**

```
Dockerfile → Image → Conteneur
```

**Cycle de vie d’un conteneur :**

* Création (`docker create`)
* Démarrage (`docker start`)
* Exécution (`docker exec`)
* Arrêt (`docker stop`)
* Suppression (`docker rm`)

---

### 5. Installation de Docker

**5.1. Prérequis :**

* Linux (Ubuntu/Debian), macOS ou Windows avec WSL2
* Droits sudo (Linux)
* Connexion internet

**5.2. Installation sur Ubuntu (20.04/22.04) :**

```
sudo apt update
sudo apt install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker $USER
```

**Redémarrer la session pour activer le groupe docker.**

**5.3. Vérification :**

```
docker --version
docker info
docker run hello-world
```

---

### 6. Travaux Pratiques

**TP 1 – Exécuter un premier conteneur :**

```
docker run hello-world
```

**Objectif :** tester que Docker fonctionne.

**TP 2 – Comparer conteneur vs VM :**

1. Lancer une VM Ubuntu via VirtualBox
2. Lancer un conteneur Alpine via :

```
docker run -it alpine sh
```

3. Comparer les ressources mémoire, CPU, taille de l’image
4. Exécuter des commandes dans les deux environnements

**TP 3 – Inspecter un conteneur NGINX :**

```
docker run -d -p 8080:80 --name webserver nginx
docker ps
docker exec -it webserver bash
```

---

### 7. Évaluation intermédiaire (QCM ou oral)

1. Quelle est la différence fondamentale entre une VM et un conteneur ?
2. À quoi sert un Dockerfile ?
3. Quelle commande permet d’exécuter un conteneur en mode interactif ?
4. Quel est le rôle du daemon Docker ?

---
Très bien, à partir de maintenant, tous les modules Docker incluront systématiquement des **travaux pratiques sous forme de fiches corrigées**, avec :

* Objectif du TP
* Étapes à suivre
* Commandes à exécuter
* Résultats attendus
* Corrigé/commentaires éventuels

Voici ci-dessous les **fiches corrigées des TP du Module 1**.

---

**FICHE TP 1 – Vérification de l’installation Docker**

**Objectif :** Vérifier que Docker fonctionne correctement sur votre système

**Étapes à suivre :**

1. Ouvrir un terminal
2. Taper la commande suivante :

```
docker run hello-world
```

**Résultat attendu :**
Un message s’affiche indiquant que le conteneur `hello-world` a été exécuté avec succès :

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

**Corrigé :**

* Si vous voyez ce message, Docker est bien installé.
* Sinon, vérifier que le service Docker est démarré : `sudo systemctl start docker`
* Vérifier les droits du groupe Docker : `groups $USER` doit contenir `docker`

---

**FICHE TP 2 – Comparaison VM vs conteneur Alpine**

**Objectif :** Comprendre la légèreté des conteneurs face à une machine virtuelle

**Étapes à suivre :**

1. Lancer une VM Ubuntu via VirtualBox (si disponible)
2. Ouvrir un terminal dans la VM et taper :

```
top
```

Observer la mémoire et le CPU utilisés.

3. Dans le système hôte, exécuter :

```
docker run -it alpine sh
```

4. À l’intérieur du conteneur Alpine, taper :

```
apk add --no-cache htop
htop
```

5. Comparer la consommation mémoire/CPU de la VM et du conteneur.

**Résultat attendu :**

* La VM consomme plusieurs centaines de Mo.
* Le conteneur Alpine consomme souvent moins de 10 Mo.

**Corrigé :**

* Les conteneurs partagent le noyau hôte, d’où leur légèreté.
* Alpine est une image ultra-légère (\~5 Mo).

---

**FICHE TP 3 – Lancer et inspecter un conteneur NGINX**

**Objectif :** Lancer un service HTTP et interagir avec son conteneur

**Étapes à suivre :**

1. Lancer NGINX en arrière-plan :

```
docker run -d -p 8080:80 --name webserver nginx
```

2. Vérifier que le conteneur tourne :

```
docker ps
```

3. Tester le service web dans le navigateur ou avec `curl` :

```
curl http://localhost:8080
```

4. Accéder au conteneur :

```
docker exec -it webserver bash
```

5. Naviguer dans `/etc/nginx`, consulter le fichier de config :

```
cat /etc/nginx/nginx.conf
```

**Résultat attendu :**

* Le site web par défaut de NGINX est servi en local sur le port 8080.
* Vous accédez au système de fichiers du conteneur.

**Corrigé :**

* L’option `-p 8080:80` redirige le port 80 interne vers le port 8080 externe.
* Le conteneur peut être arrêté avec `docker stop webserver`, puis supprimé avec `docker rm webserver`.

