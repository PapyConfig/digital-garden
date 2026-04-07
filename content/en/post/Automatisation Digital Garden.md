---
title: "How I Automated My Digital Garden with Obsidian, Hugo, and Docker"
date: 2026-04-05
draft: false
tags: [Automation, Obsidian, Hugo, Docker, CI-CD]
showToc: true
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
SOURCE_DIR = '50 - Website'
ATTACHMENTS_DIR = '03 - Resources'
TEMP_NOTES = 'processed_notes'
TEMP_IMAGES = 'processed_images'

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

    # 2. Transformation des Liens Obsidian [[Note]] -> Hugo ref
    def replace_link(match):
        note_name = match.group(1)
        clean_name = note_name.split('|')[0]
        return f'[{note_name}]({{{{< ref "{clean_name}.md" >}}}})'

    content = re.sub(r'\[\[([^\]]+)\]\]', replace_link, content)

    # 3. Transformation des Images ![[img.png]] -> Hugo path
    images = re.findall(r'!\[\[(.*?)\]\]', content)
    for img in images:
        content = content.replace(f'![[{img}]]', f'![](/images/{img})')
        img_source = os.path.join(ATTACHMENTS_DIR, img)
        if os.path.exists(img_source):
            shutil.copy(img_source, TEMP_IMAGES)

    # 4. Transformation des Callouts Obsidian -> Hugo Shortcode
    # Regex : cherche "> [!type] Titre" suivi de toutes les lignes commençant par ">"
    callout_pattern = r'(?:^> \[!(\w+)\] ?(.*)?(?:\n(?:>.*|$))*)'

    def replace_callout(match):
        kind = match.group(1).lower()
        title = match.group(2).strip() if match.group(2) else kind.capitalize()
        
        # Récupération du bloc complet et nettoyage des "> "
        full_block = match.group(0)
        lines = full_block.split('\n')
        body_lines = []
        for line in lines[1:]: # On ignore la première ligne [!type]
            body_lines.append(line.lstrip('> ').strip())
        
        body_content = " ".join(body_lines).replace('"', '\\"') # Échappement des guillemets
        
        return f'{{{{< callout kind="{kind}" title="{title}" content="{body_content}" >}}}}'

    content = re.sub(callout_pattern, replace_callout, content, flags=re.MULTILINE)

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

> [!important] Note
> Need to work on callout also

## 3. The GitHub Actions Pipeline
Deployment happens in two synchronized steps.

### Step 1: Synchronization (Obsidian Repository)
When a `push` is detected, GitHub Actions runs the Python script and pushes clean files to the Hugo repository.

> [!info] Note
> This requires a `GH_PAT` (Personal Access Token) configured in the repository secrets.

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

      - name: Push to Hugo Repo
        env:
          API_TOKEN_GITHUB: ${{ secrets.GH_PAT }}
        run: |
          git clone https://x-access-token:${API_TOKEN_GITHUB}@github.com/PapyConfig/digital-garden.git hugo-dest
          
          # Copy notes and images
          cp -r processed_notes/* hugo-dest/content/posts/
          mkdir -p hugo-dest/static/images/
          cp -r processed_images/* hugo-dest/static/images/
          
          cd hugo-dest
          git add .
          git commit -m "Sync Obsidian: $(date +'%Y-%m-%d %H:%M')" || exit 0
          git push origin main
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
    branches: [ main ]

jobs:
  build-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Hugo Repo
        uses: actions/checkout@v3
        with:
          submodules: recursive # Important for your Hugo theme

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
          extended: true

      - name: Build Hugo Site
        run: hugo --minify

      - name: Login to DockerHub (or other)
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

## 4. Why This Method?
* **Security:** My private notes never leave my private repository.
* **Distinct structure:** Although information is "duplicated," the needs are distinct and therefore scalable (adding other sources to my **Digital Garden**).
* **Automatic images:** No more manually managing the `/static/images` folder.
* **Stability:** Thanks to Docker Releases, I can revert to a previous site version in 30 seconds if a build breaks.

All that's left is to write... and click "Push"!

