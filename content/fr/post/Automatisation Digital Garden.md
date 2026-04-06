---
title: "Comment j'ai automatisé mon Digital Garden avec Obsidian, Hugo et Docker"
date: 2026-04-05
draft: false
tags: [Automation, Obsidian, Hugo, Docker, CI-CD]
showToc: true
---
# Mon Workflow : De la prise de note au déploiement Docker

Bienvenue sur ce premier article ! Pour inaugurer ce site, je vais vous expliquer comment il est généré. L'objectif était simple : **écrire dans Obsidian, faire un simple `git push`, et laisser la "magie" opérer jusqu'au déploiement final.**

Voici la structure de cette architecture et pourquoi je l'ai choisis dans ce sens.

---

## 1. L'Architecture : La séparation des pouvoirs
J'ai choisi de séparer mon environnement de travail de mon environnement de production en utilisant deux dépôts GitHub distincts :

| Dépôt                       | Rôle       | Contenu                                                                         |
| :-------------------------- | :--------- | :------------------------------------------------------------------------------ |
| **Second Brain (Privé)**    | Le cerveau | Mon Vault Obsidian complet, mes notes brutes ainsi que le script de conversion. |
| **Digital Garden (Public)** | La vitrine | Le site Hugo "propre", les thèmes et les fichiers prêts pour le build Docker.   |

---

## 2. Le script de conversion (Le traducteur)
Obsidian utilise des liens `[WikiLinks]({{< ref "WikiLinks.md" >}})` et un dossier d'images centralisé. Hugo, lui, préfère le Markdown standard. Ce script Python, placé dans le **Second Brain**, fait le pont entre les deux.

```python
import os
import re
import shutil

# Chemins
SOURCE_DIR = '50 - Digital Garden'
ATTACHMENTS_DIR = '03 - Resources'
TEMP_NOTES = 'processed_notes'
TEMP_IMAGES = 'processed_images'

# Nettoyage et création des dossiers
for d in [TEMP_NOTES, TEMP_IMAGES]:
    if os.path.exists(d): shutil.rmtree(d)
    os.makedirs(d)

def process_content(content):
    # 1. Conversion des liens [[Note]] -> [Note]({{< ref "Note.md" >}})
    content = re.sub(r'\\[\\[([^|\\]]+)\\]\\]', r'[\1]({{< ref "\1.md" >}})', content)
    # 2. Conversion images ![[img.png]] -> ![](/images/img.png)
    images = re.findall(r'!\\[\\[(.*?)\\]\\]', content)
    for img in images:
        content = content.replace(f'![[{img}]]', f'![](/images/{img})')
        if os.path.exists(os.path.join(ATTACHMENTS_DIR, img)):
            shutil.copy(os.path.join(ATTACHMENTS_DIR, img), TEMP_IMAGES)
    return content

# Execution pour chaque fichier .md
for filename in os.listdir(SOURCE_DIR):
    if filename.endswith(".md"):
        with open(os.path.join(SOURCE_DIR, filename), 'r') as f:
            content = f.read()
            if "draft: false" in content:
                new_content = process_content(content)
                with open(os.path.join(TEMP_NOTES, filename), 'w') as out:
                    out.write(new_content)
```

---

## 3. Le Pipeline GitHub Actions
Le déploiement se fait en deux étapes synchronisées.

### Étape 1 : Synchronisation (Dépôt Obsidian)
Dès qu'un `push` est détecté, GitHub Actions lance le script Python et pousse les fichiers propres vers le dépôt Hugo.

> [!info] Note
Cela nécessite un `GH_PAT` (Personal Access Token) configuré dans les secrets du repo.

```yaml
name: Push Processed Notes to Hugo
on:
  push:
    branches: [ main ]

jobs:
  sync:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Obsidian Repo
        uses: actions/checkout@v3

      - name: Process Notes
        run: python3 "03 - Resources/process_notes.py"

      - name: Pousser vers le Repo Hugo
        env:
          API_TOKEN_GITHUB: ${{ secrets.GH_PAT }}
        run: |
          git clone https://x-access-token:${API_TOKEN_GITHUB}@github.com/PapyConfig/digital-garden.git hugo-dest
          
          # Copie des notes et des images
          cp -r processed_notes/* hugo-dest/content/posts/
          mkdir -p hugo-dest/static/images/
          cp -r processed_images/* hugo-dest/static/images/
          
          cd hugo-dest
          git add .
          git commit -m "Sync Obsidian: $(date +'%Y-%m-%d %H:%M')" || exit 0
          git push origin main
```

### Étape 2 : Build & Release (Dépôt Hugo)
C'est ici que la magie Docker opère. J'utilise les **GitHub Releases** pour versionner mon site. Dès qu'un tag `v*` est créé :
1. Hugo compile le site en HTML statique (`hugo --minify`).
2. Une image Docker est construite avec **Nginx**.
3. L'image est poussée sur le Docker Hub avec le tag de versioning correspondant.

```dockerfile
# Dockerfile simple et efficace
FROM nginx:stable-alpine
COPY public/ /usr/share/nginx/html/
EXPOSE 80
```

```yaml
name: Build Hugo and Dockerize
on:
  push:
    branches: [ main ]

jobs:
  build-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Hugo Repo
        uses: actions/checkout@v3
        with:
          submodules: recursive # Important pour ton thème Hugo

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
          extended: true

      - name: Build Hugo Site
        run: hugo --minify

      - name: Login to DockerHub (ou autre)
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build and Push Docker Image
        uses: docker/build-push-action@v4
        with:
          context: .
          push: true
          tags: PapyConfig/digital-garden:latest
```

---

## 4. Pourquoi cette méthode ?
* **Sécurité :** Mes notes privées ne quittent jamais mon dépôt privé.
* **Structure distincte :** Bien que l'information soit "dupliquée", les besoins sont distincts et donc évolutif (ajout d'autres sources d'informations pour mon **Digital Garden**) 
* **Images automatiques :** Plus besoin de gérer manuellement le dossier `/static/images`.
* **Stabilité :** Grâce aux Docker Releases, je peux revenir à une version précédente du site en 30 secondes si un build casse.

Il ne me reste plus qu'à écrire... et à cliquer sur "Push" !
