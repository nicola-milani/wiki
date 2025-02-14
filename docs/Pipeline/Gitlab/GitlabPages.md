---
title: GitlabCI Pages
tags:
  - GitlabCI
  - Pipeline
  - Hugo
---

# GitlabCI
La struttura di una pipeline GitLab CI/CD è definita in un file .gitlab-ci.yml che si trova nella radice del repository.

![alt text](image.png)

Questa pipeline è configurata per generare un sito statico utilizzando Hugo e pubblicarlo su GitLab Pages. Quando viene eseguita, la pipeline:

- Utilizza l'immagine Docker registry.gitlab.com/pages/hugo/hugo_extended:latest.
- Clona i submodule Git in modo ricorsivo.
- Esegue i comandi per generare il sito e sposta i file generati nella directory public.
- Salva la directory public come artefatto.
- Esegue il job solo se il branch corrente è il branch predefinito.


Questa riga specifica l'immagine Docker da utilizzare per eseguire i job della pipeline. In questo caso, viene utilizzata un'immagine Docker preconfigurata per Hugo, un generatore di siti statici.

```yaml
image: registry.gitlab.com/pages/hugo/hugo_extended:latest
```

Questa sezione definisce le variabili di ambiente utilizzate nella pipeline. La variabile GIT_SUBMODULE_STRATEGY è impostata su recursive, il che significa che i submodule Git verranno clonati in modo ricorsivo.
```yaml
variables:
  GIT_SUBMODULE_STRATEGY: recursive
```

Job pages

Questo job è responsabile della generazione e del deploy del sito su GitLab Pages. Ecco una spiegazione dettagliata dei passaggi:

- **script**: Questa sezione contiene i comandi da eseguire per generare il sito.
    - `cd myhomewebsite && hugo`: Cambia la directory in myhomewebsite ed esegue il comando hugo per generare il sito.
    - `ls -al`: Elenca i file nella directory corrente per verificare che i file siano stati generati correttamente.
    - `mv public ../`: Sposta la directory public (contenente i file generati) nella directory superiore.
- **artifacts**: Questa sezione specifica i file e le directory da salvare come artefatti.
    - `paths`: Specifica che la directory public deve essere salvata come artefatto. Questa directory contiene i file generati che verranno serviti da GitLab Pages.
- **rules**: Questa sezione definisce le regole per l'esecuzione del job
    - `if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH`: Esegue il job solo se il branch corrente è il branch predefinito (CI_DEFAULT_BRANCH).

```yaml
image: registry.gitlab.com/pages/hugo/hugo_extended:latest
variables:
  GIT_SUBMODULE_STRATEGY: recursive
pages:
  script:
  - cd myhomewebsite && hugo
  - ls -al
  - mv public ../
  artifacts:
    paths:
    - public
  rules:
  - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
```