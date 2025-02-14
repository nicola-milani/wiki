---
title: Github Pages
tags:
  - GitHub
  - Pipeline
  - Mkdocs
---

Una pipeline GitHub Actions è definita in un file YAML che si trova nella directory workflows del repository. La pipeline è composta da uno o più job, che a loro volta contengono una serie di step. 

# Notazione "actions/"

La notazione "actions/" in GitHub Actions si riferisce a delle azioni predefinite fornite da GitHub che puoi utilizzare nei tuoi workflow. Queste azioni sono ospitate nel repository ufficiale di GitHub Actions su GitHub e sono progettate per eseguire compiti comuni come il checkout del codice, la configurazione di ambienti di runtime, il caricamento di artefatti, e molto altro.

**Sintassi generale**

La sintassi actions/<nome-azione>@<versione> indica:

```html
actions/: Il namespace che indica che l'azione è fornita da GitHub.
<nome-azione>: Il nome dell'azione.
@<versione>: La versione specifica dell'azione da utilizzare. Utilizzare una versione specifica è una buona pratica per garantire la stabilità del workflow.
```

# Esempio di workflow

## Nome del workflow
Questo è il nome del workflow e viene visualizzato nella scheda Actions del repository GitHub.

```yaml
name: Deploy mkdocs wiki pages
```

## Eventi di trigger

Questo specifica gli eventi che attivano il workflow. In questo caso, il workflow viene eseguito quando c'è un push al branch dev o quando viene avviato manualmente dalla scheda Actions.

```yaml
on:
  push:
    branches: ["dev"]
  workflow_dispatch:
```

## Permessi
Questo imposta i permessi del token GitHub (GITHUB_TOKEN) utilizzato nel workflow. Qui, il token ha permessi di lettura sui contenuti, scrittura sulle pagine e scrittura sui token ID.

```yaml
permissions:
  contents: read
  pages: write
  id-token: write
```

## Concorrenza
Questo limita il numero di esecuzioni concorrenti del workflow. 
In questo caso, permette solo una esecuzione alla volta per il gruppo "pages" e non cancella le esecuzioni in corso.

```yaml
concurrency:
  group: "pages"
  cancel-in-progress: false
```

## Valori di default 
Per esempio questo imposta la shell predefinita per i comandi di esecuzione su bash.

```yaml
defaults:
  run:
    shell: bash
```
## Job di build
Questo job esegue la build del sito. Ecco i passaggi:

**Checkout code**: Clona il repository e lo rende disponibile nello step di build

**Set up Python**: Imposta Python 3.12.

**Install dependencies**: Installa le dipendenze dal file requirements.txt.

**Setup Pages**: Configura GitHub Pages.

**Build with mkdocs**: Costruisce il sito con MkDocs eseguendo i comandi di build.

**Upload artifact**: Carica i file generati nella directory site come artefatto con il nome wiki-pages.

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.12'
      - name: Install dependencies
        run: | 
            python -m pip install --upgrade pip
            ls -al .
            pip install -r requirements.txt
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5
      - name: Build with mkdocs
        run: |
            mkdocs build
            ls -al ./site
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./site
          name: wiki-pages
```

## Job di deploy
Questo job esegue il deploy del sito su GitHub Pages.

Checkout code: Clona il repository.

Deploy to GitHub Pages: Esegue il deploy dell'artefatto wiki-pages su GitHub Pages.

```yaml
deploy:
  runs-on: ubuntu-latest
  needs: build
  steps:
    - name: Checkout code
      uses: actions/checkout@v2
    - name: Deploy to GitHub Pages
      id: deployment
      uses: actions/deploy-pages@v4
      with:
        artifact_name: wiki-pages
```
