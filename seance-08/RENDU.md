# Rendu — Séance 8

**Nom et prénom :** ADJASSEM Justin
**Identifiant GitHub :** adjassemjustin
**Date de soumission :** 07/07/2026

## Résumé de la séance

Durant cette séance, nous avons séparé la logique métier du DAG Airflow dans un module Python indépendant (`anfa_logic.py`) et écrit 5 tests unitaires avec pytest pour valider son comportement. Un pipeline CI/CD GitHub Actions a été mis en place avec deux jobs : lint (flake8) + tests unitaires, puis déploiement simulé conditionné par le succès du premier job. Nous avons démontré qu'un bug volontaire (division par 1000 au lieu de 1024 pour la conversion en Ko) fait échouer les tests et bloque automatiquement le déploiement, puis qu'après correction le pipeline passe au vert et le déploiement s'exécute.

## Étapes principales

1. Séparation de la logique métier (`anfa_logic.py`) du DAG Airflow.
2. Écriture de 5 tests unitaires avec pytest.
3. Écriture du workflow GitHub Actions (lint + tests + déploiement simulé).
4. Démonstration : un bug volontaire bloque le déploiement ; correction et succès.

## Captures d'écran

### Workflow réussi (2 jobs)
![CI succès](captures/ci-succes.png)

### Job en échec, déploiement non exécuté
![CI échec](captures/ci-echec.png)

## Réflexion personnelle

Si Mawuli avait eu ce pipeline CI/CD en place, son DAG défectueux n'aurait jamais atteint la production. Les tests unitaires auraient détecté le bug avant le merge, et le job de déploiement ne se serait pas exécuté grâce à la directive `needs: valider-dag` qui conditionne son lancement au succès complet du job de validation.

Concrètement, `needs:` crée une dépendance entre les jobs : le job `deployer` attend que `valider-dag` réussisse avant de démarrer. Si le lint ou un seul test échoue, le déploiement est automatiquement annulé. C'est un filet de sécurité qui empêche tout code non validé d'être déployé, contrairement à un déploiement manuel où l'erreur humaine est toujours possible.

