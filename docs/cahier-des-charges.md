# Cahier des charges — Spider Matrix V12 Lab

**Version :** 0.1.0  
**Statut :** proposition de spécification pour le MVP  
**Dépôt cible :** [oxxxe-dan/spider-matrix-v12-lab](https://github.com/oxxxe-dan/spider-matrix-v12-lab)  
**Périmètre initial :** analyse exploratoire reproductible, hors production et sans exécution financière.

## 1. Vision et objectifs

Spider Matrix V12 Lab est un laboratoire logiciel qui aide un utilisateur à documenter une question, analyser un jeu de données public, produire un rapport traçable et mettre en évidence les limites de l’analyse. Le système doit rendre les étapes vérifiables et reproductibles, et non présenter une sortie d’IA comme une vérité ou une prédiction certaine.

Objectifs du MVP :
- démarrer simplement, en local, avec Python et un jeu de données public de petite taille ;
- valider la structure et l’empreinte des données avant analyse ;
- générer un rapport Markdown lisible et un résumé JSON stable ;
- intégrer une critique automatique déterministe et testable ;
- recueillir les retours de pilotes avant d’ajouter des connecteurs ou agents externes.

Hors périmètre : transactions, connexion à un courtier, conseils financiers, promesses de rendement, décisions autonomes, données personnelles ou confidentielles, et affirmation de causalité à partir de corrélations.

## 2. Dépôts et documentation

- Dépôt principal : [spider-matrix-v12-lab](https://github.com/oxxxe-dan/spider-matrix-v12-lab)
- Cahier des charges : `docs/cahier-des-charges.md`
- Architecture : `docs/architecture.md`
- Flux de recherche : `docs/research-flow.md`
- README et commandes : `README.md`
- Tests automatisés : `tests/`
- Intégration continue : `.github/workflows/ci.yml`

Les contributions doivent être faites dans le dépôt principal et examinées par pull request. Ne pas créer de dépôt secondaire pour les données : les fichiers de données volumineux ou récupérables restent hors Git, sauf décision documentée. Toute extension à un autre dépôt ou service devra préciser son propriétaire, sa finalité, ses droits d’accès et les données échangées.

## 3. Utilisateurs et cas d’usage

### Utilisateurs visés
- développeurs qui mettent en place un flux d’analyse auditable ;
- analystes ou chercheurs qui souhaitent explorer un jeu de données public ;
- utilisateurs pilotes qui évaluent la clarté et l’utilité des rapports.

### Parcours MVP
1. L’utilisateur formule une question de recherche.
2. Il récupère ou fournit un CSV compatible.
3. Le système valide le schéma et calcule l’empreinte SHA-256.
4. Il exécute les statistiques exploratoires définies par le protocole.
5. La critique automatique ajoute des réserves explicites.
6. Le système enregistre les rapports Markdown et JSON.
7. L’utilisateur vérifie le rapport et transmet un retour structuré.

## 4. Données de démarrage

Le jeu de départ proposé est **UCI Wine Quality — vin rouge** (environ 1 599 lignes), un jeu public de démonstration non financier. La source, la date de récupération, le chemin du fichier et son SHA-256 sont inscrits dans un manifeste local.

Exigences données :
- ne jamais écraser silencieusement un jeu existant ;
- valider colonnes attendues, fichier non vide et types utilisables ;
- documenter la source et les conditions d’usage/attribution ;
- comparer l’empreinte du fichier avant de comparer des résultats ;
- ne pas commiter le CSV téléchargé par défaut ;
- rendre les tests indépendants d’un accès réseau, en utilisant des données synthétiques versionnées dans les tests.

L’URL de téléchargement doit être vérifiée et documentée. Le manifeste ne remplace pas une copie immuable si une reproductibilité à long terme est requise ; dans ce cas, archiver une version autorisée du dataset ou référencer une version pérenne et son empreinte connue.

## 5. Exigences fonctionnelles

### F1 — Interface de commande
La commande d’analyse accepte le chemin du dataset, le dossier de sortie et une question non vide. Les erreurs d’entrée sont lisibles et ne laissent pas de rapport partiel trompeur.

### F2 — Validation des données
Le programme vérifie le schéma requis, la présence de lignes, la conversion numérique des variables analysées et les valeurs manquantes/non finies. Il rapporte les anomalies et applique une politique explicite ; il ne supprime pas automatiquement des observations sans le signaler.

### F3 — Analyse déterministe
Le MVP calcule des statistiques descriptives et des corrélations de Pearson avec la variable `quality`. Le rapport décrit la méthode, les données utilisées, les paramètres et les limitations. Les valeurs sont arrondies uniquement pour l’affichage ou la sérialisation prévue.

### F4 — Rapports
Le JSON est la sortie structurée de référence et inclut `schema_version`, la version logicielle, l’empreinte du dataset, les paramètres, les métriques et les constats critiques. Le Markdown est une présentation dérivée de ces résultats. Les rapports n’incluent pas d’horodatage variable dans les résultats analytiques ; les métadonnées de téléchargement peuvent avoir une date distincte.

### F5 — Critique automatique
La première version est un module déterministe, pas un agent conversationnel. Il signale au minimum : corrélation ≠ causalité, absence de validation indépendante, valeurs manquantes ou constantes, associations faibles, doublons et taille/limites du jeu si applicables. Chaque règle possède un test. Les constats sont des avertissements, pas des preuves statistiques.

### F6 — Versionnage et reproductibilité
Chaque analyse peut être identifiée par un manifeste de run comprenant : version du schéma de rapport, version du code, SHA-256 des données, paramètres normalisés, méthode/statistique utilisée et versions de dépendances disponibles. Deux exécutions identiques doivent produire le même JSON et le même contenu analytique. Les environnements seront verrouillés par un fichier de lock commité après génération et vérification.

### F7 — Retour des pilotes
Un formulaire ou modèle de retour permet de noter compréhension, utilité, confiance, anomalies et suggestions. Les retours ne doivent pas contenir de données sensibles ; toute collecte doit être volontaire et documentée.

## 6. Exigences non fonctionnelles

- **Simplicité :** installation locale avec Python 3.12 et `uv` ; dépendances limitées.
- **Portabilité :** tests et CI sur environnement Linux ; éviter les chemins et paramètres dépendant de la machine.
- **Traçabilité :** sources, empreintes, version, paramètres et limites visibles.
- **Confidentialité :** aucune téléversement des datasets ou rapports ; les données restent locales dans le MVP.
- **Sécurité :** permissions minimales dans CI, dépendances raisonnables, aucun secret requis pour les tests, entrées traitées comme non fiables.
- **Accessibilité :** rapports en texte structuré, lisibles sans visualisation ni service externe.
- **Robustesse :** échec contrôlé pour fichiers mal formés, colonnes manquantes, données vides ou valeurs non numériques.

## 7. Architecture cible du MVP

`CLI → validation/chargement → calculs déterministes → critique automatique → rapport JSON → rendu Markdown`

Modules proposés :
- `dataset.py` : lecture, validation et empreinte ;
- `pipeline.py` : orchestration et calculs ;
- `critique.py` : règles de critique déterministes ;
- `report.py` (à séparer si le pipeline grossit) : sérialisation et rendu ;
- `cli.py` : interface utilisateur ;
- `scripts/` : récupération explicite du dataset ;
- `tests/` : tests unitaires, intégration et cas d’évaluation.

Le système ne dépend pas d’un LLM pour calculer des statistiques. Une future intégration d’agent doit être optionnelle, clairement étiquetée, isolée du calcul, ne pas avoir d’accès réseau ou d’écriture non nécessaire et fournir des références vérifiables.

## 8. Plan qualité et évaluation

### Tests automatisés minimaux
- schéma valide, colonnes manquantes, CSV absent/vide/mal formé ;
- données constantes, valeurs manquantes et corrélations non calculables ;
- calculs contrôlés sur de petits jeux synthétiques ;
- empreinte SHA-256 stable ;
- stabilité des rapports sur deux exécutions identiques ;
- vérification des réserves de critique ;
- tests CLI et messages d’erreur ;
- aucun test ne nécessite le réseau.

### Évaluations du rapport
Pour un ensemble de cas de test revu par un humain, vérifier :
- exactitude des valeurs par rapport aux calculs de référence ;
- présence de la source, empreinte, méthode et version ;
- présence des limites attendues ;
- absence de formulation causale ou de promesse prédictive non justifiée ;
- cohérence entre le rapport JSON et le Markdown.

Les seuils d’acceptation doivent être fixés avant d’évaluer une future version d’agent. Les évaluations ne doivent pas être optimisées uniquement pour un score automatisé.

## 9. CI, livraison et revue

À chaque pull request et push : installation des dépendances verrouillées, lint, tests et vérification de format. Les permissions du workflow restent minimales (`contents: read`). Toute nouvelle dépendance ou action CI doit être justifiée et maintenue.

Une livraison MVP est acceptable si :
- le démarrage documenté fonctionne sur un environnement propre ;
- les tests et le lint passent ;
- les rapports sont traçables et reproductibles ;
- les cas invalides échouent sans produire de résultat trompeur ;
- les limites et le périmètre sans trading sont clairement visibles.

## 10. Protocole pilote

### Préparation
- recruter un petit groupe diversifié de volontaires ;
- fournir le même dataset public et une tâche de recherche définie ;
- expliquer que le prototype est exploratoire et ne constitue pas un conseil ;
- préparer un questionnaire sans collecte de données sensibles.

### Séance
1. Demander au participant d’installer et lancer le flux avec la documentation.
2. Lui faire interpréter le rapport sans aide, en observant les incompréhensions.
3. Lui demander d’identifier la source, l’empreinte, la méthode et les limites.
4. Recueillir les problèmes, le temps nécessaire et les améliorations proposées.

### Critères de passage
- les utilisateurs parviennent à exécuter le parcours avec la documentation ;
- ils distinguent corrélation et causalité ;
- ils identifient les limites du dataset et l’empreinte du run ;
- aucune anomalie bloquante ou fuite de données n’est observée ;
- les retours sont synthétisés et les problèmes critiques corrigés avant intégration élargie.

Le nombre de participants et les seuils chiffrés seront convenus avant le pilote ; ne pas présenter un petit test comme une validation statistique du produit.

## 11. Feuille de route et critères de sortie

### Phase A — Socle
Structure du dépôt, environnement verrouillé, docs, CI, jeu synthétique de tests.

### Phase B — Flux de recherche
Téléchargement explicite et manifeste, validation du dataset, analyse simple, rapports JSON/Markdown.

### Phase C — Reproductibilité et critique
Version de schéma, empreintes, stabilité des rapports, règles de critique et tests d’évaluation.

### Phase D — Pilote utilisateur
Guide de test, collecte de retours volontaire, synthèse des résultats et corrections.

### Phase E — Décision d’extension
Évaluer la valeur d’un agent critique LLM, d’autres datasets ou connecteurs uniquement après revue de confidentialité, sécurité, coût, qualité et conditions d’utilisation. Ne pas activer d’exécution réelle dans le périmètre de ce cahier des charges.

## 12. Protocole d’évolution

Toute modification du protocole doit :
1. expliquer le besoin et l’impact utilisateur ;
2. préciser les exigences et critères d’acceptation touchés ;
3. ajouter ou mettre à jour les tests et cas d’évaluation ;
4. documenter les changements de schéma et de reproductibilité ;
5. faire l’objet d’une revue avant fusion ;
6. mettre à jour ce document avec un historique de changements.

### Historique
- `0.1.0` — définition initiale du périmètre MVP, du flux reproductible, de la critique automatique et du protocole pilote.
