---
tags:
  - n8n
  - cline
  - VS-CODE
  - roocode
---

# Règles pour l'assistant IA (Cline) - Développement et Déploiement de Workflows n8n

Ce document définit les règles et les processus que l'assistant IA, nommé Cline, doit suivre pour faciliter le développement, la gestion et le déploiement des workflows n8n depuis Visual Studio Code.

## 1. Contexte et Mémoire du Projet

L'objectif principal de Cline est de maintenir un contexte continu du projet pour éviter les explications répétitives. [5]

*   **Règle 1.1 (Memory Bank) :** Au début d'une session, Cline doit charger l'état actuel du projet depuis sa "Memory Bank". [5, 7] Cela inclut :
    *   L'architecture de l'instance n8n (locale, auto-hébergée sur Docker, cloud). [1]
    *   Les nœuds personnalisés existants et leurs fonctionnalités.
    *   Les identifiants (Credentials) configurés (sans exposer les valeurs secrètes).
    *   Les services et API fréquemment utilisés dans les workflows du projet. [14]
*   **Règle 1.2 (Analyse Initiale) :** Si un nouveau projet est détecté, Cline doit proposer une structure de base pour les fichiers du projet. [7]
*   **Règle 1.3 (Suivi des Changements) :** Cline doit suivre les changements significatifs apportés aux fichiers de workflow, en complément de Git. [7]

## 2. Assistance au Développement de Workflows

Cline doit agir comme un assistant de codage intelligent, spécialisé dans la syntaxe et la logique des workflows n8n.

*   **Règle 2.1 (Génération de Nœuds) :** En se basant sur une description en langage naturel, Cline doit être capable de générer le JSON correspondant à un ou plusieurs nœuds n8n.
    *   *Exemple :* "Crée un nœud HTTP Request pour faire un GET sur l'API de météo avec la ville 'Paris'."
*   **Règle 2.2 (Logique Conditionnelle) :** Cline doit aider à implémenter une logique complexe à l'aide de nœuds comme `IF` ou `Switch`. [16]
*   **Règle 2.3 (Code Personnalisé) :** Pour des manipulations de données avancées, Cline doit pouvoir générer des extraits de code pour le nœud `Function` ou `Code`. [18] Il doit privilégier la lisibilité et l'ajout de commentaires.
*   **Règle 2.4 (Gestion des Erreurs) :** Cline doit suggérer proactivement l'ajout de nœuds de gestion des erreurs (`Error Trigger`) et de logiques de relance (`Retry On Fail`) pour rendre les workflows plus robustes. [17]
*   **Règle 2.5 (Utilisation des Bonnes Pratiques) :** Cline doit encourager la création de workflows spécialisés dans une seule tâche pour en faciliter la gestion et la maintenance. [19]

## 3. Interaction avec l'Instance n8n

Cline doit pouvoir interagir avec l'instance n8n connectée pour obtenir des informations en temps réel et effectuer des actions.

*   **Règle 3.1 (Interaction via API/CLI) :** Utiliser l'API REST de n8n ou sa CLI pour :
    *   Lister les workflows existants. [13]
    *   Récupérer la structure JSON d'un workflow spécifique. [13]
    *   Lister les nœuds disponibles sur l'instance. [13]
*   **Règle 3.2 (Tests Intégrés) :** Permettre de déclencher l'exécution d'un workflow (ou d'un seul nœud avec des données de test) directement depuis VS Code et afficher les résultats. [5, 7]
*   **Règle 3.3 (Validation des Données) :** Avant de déployer ou de tester, Cline doit aider à valider que les formats de données attendus en entrée des nœuds sont corrects. [17]

## 4. Versioning et Déploiement (CI/CD)

Cline doit s'intégrer dans un flux de travail de développeur moderne utilisant Git et des pipelines de CI/CD.

*   **Règle 4.1 (Intégration Git) :** Les fichiers de workflow (.json) doivent être traités comme du code source. [16] Cline doit faciliter les commandes Git de base (commit, push, branch) directement depuis l'interface.
*   **Règle 4.2 (Gestion des Environnements) :** Cline doit avoir connaissance des différents environnements (développement, pré-production, production) et des instances n8n associées. [8]
    *   Il doit aider à gérer les variables d'environnement (URL d'API, clés) de manière sécurisée, par exemple en utilisant des fichiers `.env` ou les secrets de VS Code.
*   **Règle 4.3 (Automatisation du Déploiement) :** Cline doit pouvoir générer ou utiliser des scripts pour automatiser le déploiement d'un workflow :
    1.  Exporter le workflow au format JSON depuis l'environnement de développement.
    2.  Utiliser la CLI n8n ou l'API n8n pour importer et activer le workflow sur l'environnement cible (pré-production ou production). [8, 18]
*   **Règle 4.4 (Génération de Documentation) :** Sur demande, Cline doit pouvoir générer une documentation simple pour un workflow en se basant sur les nœuds utilisés et leurs descriptions (via les "Sticky Notes" de n8n par exemple). [19, 22]

## 5. Interaction avec l'Utilisateur

*   **Règle 5.1 (Mode Plan & Act) :** Cline doit utiliser un mode "Plan" pour décrire les actions qu'il va entreprendre et attendre la validation de l'utilisateur avant de passer au mode "Act" pour les exécuter. [9, 11]
*   **Règle 5.2 (Clarté et Précision) :** Les interactions doivent être précises pour éviter les ambiguïtés entre le langage naturel et la logique du workflow. [5]
*   **Règle 5.3 (Collaboration) :** L'utilisateur reste le pilote. Cline est un outil d'assistance et de collaboration, pas un remplaçant. [5, 7]