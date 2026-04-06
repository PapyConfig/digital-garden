# Digital Garden

Site statique construit avec Hugo en utilisant le thème `heyo`.

Ce dépôt contient le contenu, le thème et la configuration pour générer le site statique. Le site généré est exporté dans le dossier `public/` et peut être servi par Nginx via le `Dockerfile` fourni.

## Raccourci des commandes

- Générer le site localement (Hugo must be installed):

```bash
hugo server -D
```

- Construire le site (production):

```bash
hugo --minify
```

- Construire l'image Docker localement et la lancer:

```bash
# build
docker build -t your-username/digital-garden:local .
# run (serve on port 8080)
docker run --rm -p 8080:80 your-username/digital-garden:local
```

## CI / GitHub Actions

Un workflow GitHub Actions (`.github/workflows/build-and-dockerize.yml`) est inclus pour :

- Construire le site Hugo
- Construire et pousser une image Docker vers GitHub Container Registry (`ghcr.io`)

Assurez-vous d'avoir configuré les secrets nécessaires :

- `CR_PAT` : un Personal Access Token avec `write:packages` (ou utilisez `GITHUB_TOKEN` si autorisé).

Le workflow tagge l'image avec `:latest` et avec le SHA du commit.

## Docker

Le `Dockerfile` inclus sert le contenu statique généré (`public/`) via Nginx :

```dockerfile
FROM nginx:stable-alpine
COPY public/ /usr/share/nginx/html/
EXPOSE 80
```

Important : build du site (`hugo --minify`) doit être exécuté avant la construction de l'image, afin que `public/` contienne les fichiers statiques.

## Contribution

- Pour proposer des modifications, ouvrez une pull request.
- Merci de suivre le style et la structure du contenu existant.

## Licence

Consultez le fichier `LICENSE` à la racine du dépôt.

---

Si vous voulez que j'ajoute une section supplémentaire (déploiement, preview, ou instructions locales détaillées), dites-moi quoi inclure.
