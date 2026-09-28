# devops-training

Notes de cours DevOps et l'application fil rouge qui sert à les mettre en
pratique.

## Contenu

| Fichier | Sujet |
|---|---|
| `01-pipeline-cicd.md` | ce qu'est un pipeline CI/CD, les étages, les principes |
| `02-github-actions.md` | GitHub Actions : workflows, jobs, actions réutilisables |
| `.github/` | le pipeline mis en place pour de vrai sur ce dépôt |
| `app/` | l'application de démonstration |

## Le pipeline

`.github/workflows/ci.yml` — « Full DevOps Pipeline », sur `push` et
`pull_request` vers `main`. Il appelle l'action composite locale
`.github/actions/setup-tools`, qui installe Terraform et vérifie docker et
kubectl, puis passe le dépôt au scanner **Trivy**.

`.github/workflows/reusable-docker.yml` est un workflow réutilisable
(`workflow_call`) : on lui donne un nom d'image, un contexte et un Dockerfile,
il construit l'image avec le cache GitHub Actions et rend le tag en sortie.
C'est l'exercice sur la factorisation des workflows.

## L'application fil rouge

Une petite API Node sans dépendance (`app/src/index.js`), avec trois points
d'entrée :

- `/` — une réponse JSON simple
- `/health` — le healthcheck appelé par Docker et Kubernetes
- `/metrics` — des métriques au format texte Prometheus

`app/src/math.js` porte la logique testée par `app/test/math.test.js`, exécuté
par le lanceur de tests intégré à Node (`node --test`), sans framework.

```bash
cd app
npm test
npm start
docker build -t devops-app:1.0.0 .
```

Le `Dockerfile` et le `.dockerignore` sont dans `app/`.

## Où ça continue

Cette application est le point de départ de **[Locatic](https://github.com/by-teenspirit/Locatic)**,
qui reprend la même chaîne — CI GitHub Actions, image Docker, Terraform,
Ansible, Kubernetes — mais autour d'une vraie application .NET 8.
