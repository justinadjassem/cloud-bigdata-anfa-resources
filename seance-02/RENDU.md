# Rendu - Seance 2

**Nom et prenom :** ADJASSEM Yao-fiawomo Justin
**Identifiant GitHub :** justinadjassem
**Date de soumission :** 23/06/2026

## Resume de la seance

Ecriture d'un Dockerfile pour containeriser le script d'analyse du referentiel Anfa, construction de l'image Docker `anfa-analyse:v1`, orchestration d'une stack de 3 services (MinIO, Jupyter, anfa-app) via Docker Compose, et exploration des donnees depuis un notebook Jupyter connecte a MinIO.

## Etapes principales

1. Ecriture du Dockerfile et construction de l'image `anfa-analyse:v1` (taille observee : 702 Mo).
2. Mise en place du `.dockerignore` et observation du cache de Docker.
3. Ecriture du `docker-compose.yml` orchestrant MinIO, Jupyter, et l'image custom.
4. Creation du notebook `exploration_minio.ipynb` qui lit les donnees depuis MinIO via boto3 et pandas.

## Captures d'ecran

### docker compose ps
![docker compose ps](captures%20d'%C3%A9cran/capture%20d'%C3%A9cran%20de%20docker%20compose%20ps.png)

### Notebook Jupyter
![Notebook Jupyter](captures%20d'%C3%A9cran/capture%20d'%C3%A9cran%20notebook.png)

## Bonus multi-stage (optionnel)

Le Dockerfile multi-stage (`Dockerfile.multistage`) a ete ecrit mais le build n'a pas pu aboutir en raison d'une erreur reseau temporaire avec Docker Hub (EOF lors du pull de l'image de base). La comparaison de taille v1 vs v2-multistage sera effectuee une fois la connexion retablie.

## Difficultes rencontrees

- Le volume MinIO etait vide (nouveau volume) : il a fallu recreer le bucket `anfa-raw`, la cle applicative, et relancer le script d'upload de la seance 1.
- Les ports des services (MinIO console, Jupyter) n'etaient pas exposes dans le `docker-compose.yml` initial, empechant l'acces depuis le navigateur.
- Erreur reseau temporaire lors du build multi-stage (`EOF` sur le registry Docker Hub).
