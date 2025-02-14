# GitlabCI
La struttura di una pipeline GitLab CI/CD è definita in un file .gitlab-ci.yml che si trova nella radice del repository.


Definisce le fasi della pipeline: build e deploy.
```yaml
stages:
  - build
  - deploy
```
Definisce le variabili di ambiente utilizzate nella pipeline.
```yaml
variables:
  VERSION: "1.0.0"
  PYTHON_VERSION: "3.12"
```
Comandi eseguiti prima di ogni job per installare le dipendenze necessarie.

```yaml
before_script:
  - apt-get update
  - apt-get install -y python3-pip
```
Questo job esegue la build del sito:

Installa le dipendenze.

- Esegue il comando mkdocs build.
- Salva i file generati nella directory site come artefatti.

```yaml
build:
  stage: build
  script:
    - python3 -m pip install --upgrade pip
    - ls -al .
    - pip3 install -r requirements.txt
    - mkdocs build
    - ls -al ./site
  artifacts:
    paths:
      - ./site
```

Questo job esegue il deploy del sito:

- Installa Git.
- Configura Git.
- Clona il branch gh-pages.
- Copia i file generati nella directory public.
- Esegue il commit e il push delle modifiche.

```yaml
```yaml
deploy:
  stage: deploy
  script:
    - mkdir -p public
    - cp -r ./site/* public/
  artifacts:
    paths:
      - public
  only:
    - dev

```