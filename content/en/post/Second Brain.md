---
title: "My Second Brain"
date: 2026-04-05
draft: false
tags: [Obsidian, Markdown, Self-Driving, Documentation]
categories: [LifeOS]
showToc: false
badges:
  github:
    flat: false
    subject: GitHub
    status: Private
    icon: github
    url: https://github.com/PapyConfig/second-brain
    color: grey
    label: ""
thumbnail:
  src: 'images/second-brain.jpg'
  alt: 'second brain with ai'
  object_position: '50% 50%'
  height: 250px
images: ['images/second-brain.jpg']
---

## Architecture Overview

My second-brain is built on a three-layer architecture combining note-taking, static site generation, and containerized deployment.

## Interaction Workflow

1. **Capture**: Notes are written in Obsidian with a consistent markdown structure
2. **Process**: Automated scripts validate and organize content
3. **Publish**: Hugo transforms markdown into static HTML
4. **Deploy**: Docker containers handle CI-CD pipeline and hosting

## Technical Pipeline

The workflow is triggered on git commits, flowing through automated tests, builds, and deployment stages. This ensures consistency and reduces manual intervention.

