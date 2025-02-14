
Una pipeline GitHub Actions è definita in un file YAML che si trova nella directory workflows del repository. La pipeline è composta da uno o più job, che a loro volta contengono una serie di step. 

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
