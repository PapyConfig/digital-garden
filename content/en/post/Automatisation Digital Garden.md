---
title: "How I Automated My Digital Garden with Obsidian, Hugo, and Docker"
date: 2026-04-05
draft: false
tags: [Automation, Obsidian, Hugo, Docker, CI-CD]
categories: [Homelab]
showToc: true
badges:
  github:
    flat: false
    subject: GitHub
    status: Check it on GitHub
    icon: github
    url: https://github.com/PapyConfig/digital-garden
    color: grey
    label: ""
thumbnail:
    src: 'images/digital-garden.jpg'
    alt: 'digital (futuriste/solarpunk) garden with a French old man'
    object_position: '50% 50%'
    height: 250px
images: ['images/digital-garden.jpg']
---
# My Workflow: From Note-Taking to Docker Deployment

Welcome to this first article! To inaugurate this site, I'll explain how it's generated. The goal was simple: **write in Obsidian, do a simple `git push`, and let the "magic" happen until final deployment.**
<!--more-->

Here's the architecture of this system and why I chose it this way.

---

## 1. The Architecture: Separation of Powers
I chose to separate my work environment from my production environment using two distinct GitHub repositories:

| Repository                  | Role       | Content                                                                        |
| :-------------------------- | :--------- | :----------------------------------------------------------------------------- |
| **Second Brain (Private)**  | The brain  | My complete Obsidian Vault, raw notes, and conversion script.                  |
| **Digital Garden (Public)** | The showcase | The clean Hugo site, themes, and files ready for Docker builds.               |

---

## 2. The Conversion Script (The Translator)
Obsidian uses `[WikiLinks]({{< ref "WikiLinks.md" >}})` and a centralized image folder. Hugo prefers standard Markdown. This Python script, placed in the **Second Brain**, bridges the two.

```python
import os
import re
import shutil

# Configuration
BASE_DIR = os.path.dirname(os.path.abspath(__file__))
ROOT_DIR = os.path.abspath(os.path.join(BASE_DIR, os.pardir))
SOURCE_DIR = os.path.join(ROOT_DIR, '50 - Digital Garden')
ATTACHMENTS_DIR = os.path.join(ROOT_DIR, '03 - Resources')
TEMP_NOTES = os.path.join(ROOT_DIR, 'processed_notes')
TEMP_IMAGES = os.path.join(ROOT_DIR, 'processed_images')

# Vérifie que les dossiers source existent
if not os.path.isdir(SOURCE_DIR):
    raise FileNotFoundError(f"SOURCE_DIR introuvable : {SOURCE_DIR}")
if not os.path.isdir(ATTACHMENTS_DIR):
    raise FileNotFoundError(f"ATTACHMENTS_DIR introuvable : {ATTACHMENTS_DIR}")

# Nettoyage et création des dossiers de base
for d in [TEMP_NOTES, TEMP_IMAGES]:
    if os.path.exists(d): shutil.rmtree(d)
    os.makedirs(d)

def process_content(content):
    """Transformations complètes : Code, Callouts, Liens, Images."""
    
    # 1. Protection des blocs de code (``` ... ```)
    code_blocks = []
    def save_code_block(match):
        code_blocks.append(match.group(0))
        return f"%%CODE_BLOCK_{len(code_blocks)-1}%%"

    content = re.sub(r'```[\s\S]*?```', save_code_block, content)

    # 2. Transformation des Images ![[img.png]] -> Hugo path
    images = re.findall(r'!\[\[(.*?)\]\]', content)
    for img in images:
        content = content.replace(f'![[{img}]]', f'![](/images/{img})')
        img_source = os.path.join(ATTACHMENTS_DIR, img)
        if os.path.exists(img_source):
            shutil.copy(img_source, TEMP_IMAGES)

    # 3. Transformation des Liens Obsidian [[Note]] -> Hugo ref
    def replace_link(match):
        note_name = match.group(1)
        clean_name = note_name.split('|')[0]
        return f'[{note_name}]({{{{< ref "{clean_name}.md" >}}}})'

    content = re.sub(r'\[\[([^\]]+)\]\]', replace_link, content)

    # 4. Transformation des Callouts Obsidian -> Hugo Shortcode
    def replace_callout(match):
        block = match.group(1)
        lines = block.split('\n')
        if not lines:
            return block

        first_line = lines[0].lstrip('> ').strip()
        if not first_line.startswith('[!') or ']' not in first_line:
            return block

        type_end = first_line.find(']')
        kind = first_line[2:type_end].lower()
        title_part = first_line[type_end+1:].strip()
        title = title_part if title_part else kind.capitalize()

        body_lines = []
        for line in lines[1:]:
            if line.startswith('> '):
                body_lines.append(line[2:].rstrip())
            elif line.startswith('>'):
                body_lines.append(line[1:].rstrip())
            else:
                body_lines.append(line.rstrip())

        body_content = ' '.join(line.strip() for line in body_lines if line.strip()).replace('"', '\\"')
        return f'{{{{< callout kind="{kind}" title="{title}" content="{body_content}" >}}}}'

    callout_pattern = re.compile(r'(^> \[![^\]\n]+\].*(?:\n(?!\s*$).*)*)', flags=re.MULTILINE)
    content = callout_pattern.sub(replace_callout, content)

    # 5. Restauration des blocs de code
    for i, block in enumerate(code_blocks):
        content = content.replace(f"%%CODE_BLOCK_{i}%%", block)
        
    return content

# Exploration récursive
for dirpath, dirnames, filenames in os.walk(SOURCE_DIR):
    for filename in filenames:
        if filename.endswith(".md"):
            file_path = os.path.join(dirpath, filename)
            with open(file_path, 'r', encoding='utf-8') as f:
                content = f.read()
                new_content = process_content(content)
                
                rel_dir = os.path.relpath(dirpath, SOURCE_DIR)
                dest_dir = os.path.join(TEMP_NOTES, rel_dir)
                os.makedirs(dest_dir, exist_ok=True)
                
                with open(os.path.join(dest_dir, filename), 'w', encoding='utf-8') as out:
                    out.write(new_content)

print("✅ Terminé : Callouts convertis et fichiers MD traités.")
```

## 3. The GitHub Actions Pipeline
Deployment happens in two synchronized steps.

### Step 1: Synchronization (Obsidian Repository)
When a `push` is detected, GitHub Actions runs the Python script and pushes clean files to the Hugo repository.

{{< callout kind="info" title="Note" content="This requires a `GH_PAT` (Personal Access Token) configured in the repository secrets." >}}

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

      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.x'

      - name: Process Notes
        run: python3 "03 - Resources/process_notes.py"

      - name: Pousser vers le Repo Hugo
        env:
          API_TOKEN_GITHUB: ${{ secrets.CR_PAT }}
        run: |
          set -e
          git config --global user.email "actions@github.com"
          git config --global user.name "github-actions[bot]"
          
          # Clone du repo cible
          git clone --depth 1 https://x-access-token:${API_TOKEN_GITHUB}@github.com/PapyConfig/digital-garden.git hugo-dest
          
          # 1. NETTOYAGE SÉCURISÉ
          # On supprime le contenu, mais on ne s'arrête pas si le dossier n'existe pas
          rm -rf hugo-dest/content 2>/dev/null || true
          
          # 2. RÉCRÉATION DES DOSSIERS
          # Le -p permet de créer toute l'arborescence sans erreur
          mkdir -p hugo-dest/content
          
          # 3. COPIE DES DONNÉES TRAITÉES
          # On copie le contenu de nos dossiers temporaires vers le repo Hugo
          cp -a processed_notes/. hugo-dest/content/
          cp -a processed_images/. hugo-dest/static/images/
          
          # 4. ENVOI
          cd hugo-dest
          git add .
          # On évite de planter si rien n'a changé
          if git diff --staged --quiet; then
            echo "Rien à committer, le jardin est déjà à jour."
            exit 0
          fi
          git commit -m "Sync Obsidian (with callouts): $(date +'%Y-%m-%d %H:%M')"
          git push origin master
```

### Step 2: Build & Release (Hugo Repository)
This is where Docker magic happens. I use **GitHub Releases** to version my site. When a `v*` tag is created:
1. Hugo compiles the site to static HTML (`hugo --minify`).
2. A Docker image is built with **Nginx**.
3. The image is pushed to Docker Hub with the corresponding version tag.

```dockerfile
# Simple and efficient Dockerfile
FROM nginx:stable-alpine
COPY public/ /usr/share/nginx/html/
EXPOSE 80
```

```yaml
name: Build Hugo and Dockerize
on:
  push:
    branches: [ master ]

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: digital-garden
  USER_LOWER: papyconfig

jobs:
  build-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Hugo Repo
        uses: actions/checkout@v3
        with:
          submodules: recursive # Important pour ton thème Hugo
          fetch-depth: 0

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
          extended: true

      - name: Build Hugo Site
        run: hugo --minify

      - name: Login to GHCR
        uses: docker/login-action@v2
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.CR_PAT }}

      - name: Build and Push Docker Image
        uses: docker/build-push-action@v4
        with:
          context: .
          file: DOCKERFILE
          push: true
          labels: |
            org.opencontainers.image.source=https://github.com/${{ github.repository }}
          tags: |
            ${{ env.REGISTRY }}/${{ env.USER_LOWER }}/${{ env.IMAGE_NAME }}:latest
            ${{ env.REGISTRY }}/${{ env.USER_LOWER }}/${{ env.IMAGE_NAME }}:${{ github.sha }}

      - name: Trigger Webhook
        run: curl -X GET -u "n8n:${{ secrets.WEBHOOK_PASSWORD }}" "https://n8n.rohmer.beer/webhook/762e85af-325b-4d5d-8729-1835dd0ca177"
```

{{< callout kind="info" title="Webhook" content="You will notice that I use a webhook to restart a docker-compose stack to repull the latest available image of the Digital Garden to automatically update the website." >}}

---

## 4. Why This Method?
* **Security:** My private notes never leave my private repository.
* **Distinct structure:** Although information is "duplicated," the needs are distinct and therefore scalable (adding other sources to my **Digital Garden**).
* **Automatic images:** No more manually managing the `/static/images` folder.
* **Stability:** Thanks to Docker Releases, I can revert to a previous site version in 30 seconds if a build breaks.

All that's left is to write... and click "Push"!

