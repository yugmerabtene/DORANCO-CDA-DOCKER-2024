* Séparation des branches (`main` protégée, `dev` pour coder)
* Lancement automatique du pipeline en cas de merge
* Tests automatisés
* Build d’image Docker
* Push sur Docker Hub ou GitHub Container Registry
* Déploiement automatique sur un VPS via SSH

---
## 🎯 Objectif

Je souhaite mettre en place un **pipeline CI/CD complet et sécurisé**, applicable à n’importe quel projet (PHP, Python, Node.js, Java, etc.), avec les exigences suivantes :

---

## 🔁 Flux de développement

* `dev` : branche de développement active
* `main` : **branche protégée**, **aucun push direct autorisé**
* Le pipeline CI/CD se déclenche uniquement **lors d’un merge vers `main`**

---

## 🔐 Sécurité Git

* **Protection de `main`** :

  * ❌ Interdiction de `git push` direct
  * ✅ Obligation de passer par **Pull Request depuis `dev`**
  * ✅ Les **tests doivent réussir** avant autorisation de merge
  * ✅ Historique linéaire requis (`Require linear history`)
  * ✅ Optionnel : **review obligatoire d’un développeur**

---

## ⚙️ CI/CD Pipeline attendu

### ✅ CI (Continuous Integration)

* Lancer automatiquement les **tests unitaires**
* Build d’une **image Docker** du projet
* (Optionnel) Vérification de style/code qualité (lint, audit…)

### ✅ CD (Continuous Deployment)

* Push de l’image Docker vers :

  * Docker Hub
  * ou GitHub Container Registry (`ghcr.io`)
* Connexion SSH à un **serveur VPS Linux distant**
* Arrêt de l’ancien conteneur (si existant)
* Pull de la nouvelle image Docker
* Lancement automatique d’un nouveau conteneur (`docker run -d`)
* Redémarrage automatique (`--restart always`)

---

## 🧱 Structure type du projet

```
mon-projet/
├── src/ (code)
├── tests/ (unitaires)
├── public/ ou app/
├── Dockerfile
├── .dockerignore
├── .github/workflows/cicd.yml
├── docker-compose.yml (facultatif)
├── README.md
└── fichier de configuration du langage (.env, composer.json, etc.)
```

---

## 📦 Secrets attendus (dans GitHub ou GitLab)

| Nom               | Description                            |
| ----------------- | -------------------------------------- |
| `DOCKER_USERNAME` | Identifiant Docker Hub                 |
| `DOCKER_PASSWORD` | Mot de passe ou token                  |
| `SERVER_IP`       | IP publique du serveur VPS             |
| `SERVER_USER`     | Utilisateur SSH (ex: `root`, `ubuntu`) |
| `SERVER_SSH_KEY`  | Clé privée SSH (non chiffrée)          |

---

## 🚀 Exécution automatique

* Un `git push origin dev`
* Une **Pull Request dev → main**
* Si les tests passent ✅ et PR validée ✅ → merge
* GitHub Actions déclenche alors :

  * tests + build + push Docker + déploiement sur VPS
