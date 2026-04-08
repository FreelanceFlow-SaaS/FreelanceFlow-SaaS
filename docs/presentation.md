Projet CI/CD — FreelanceFlow
Contexte
Vous êtes chargés de concevoir une application web permettant à un freelance de gérer son activité commerciale. 
Vous serez également évalués sur votre capacité à organiser, industrialiser, tester, conteneuriser et déployer votre application avec une démarche CI/CD professionnelle.

Objectifs pédagogiques
Ce projet doit vous permettre de démontrer votre capacité à :
travailler en équipe avec une répartition claire des rôles
concevoir une application web complète avec frontend et backend
organiser un backlog et suivre un projet avec GitHub Projects
mettre en place une stratégie Git cohérente
garantir la qualité du code
écrire et exécuter des tests unitaires
conteneuriser les services avec Docker
automatiser les contrôles avec une pipeline CI
mettre l’application en ligne
gérer des tags et des releases

Organisation
Projet à réaliser en équipe de 4 :
un/des profil frontend
un/des profil backend
un profil devops
Chaque étudiant garde un rôle principal, mais l’équipe est collectivement responsable du bon fonctionnement de l’application et de sa livraison.

Sujet
Développer une application nommée FreelanceFlow permettant à un freelance de :
gérer ses clients
créer des prestations
créer une facture
télécharger les documents au format PDF

Fonctionnalités minimales attendues pour la V1
Authentification

L’utilisateur (le freelance) doit pouvoir : 
Créer un compte
Se loguer
Gestion des clients
L’utilisateur doit pouvoir :
créer un client
consulter la liste des clients
modifier un client
supprimer un client
Un client contient  :
nom
email
entreprise
adresse

Gestion des prestations
L’utilisateur doit pouvoir :
définir un intitulé
un tarif heure

Gestion des factures
L’utilisateur doit pouvoir :
créer une facture à partir d’un client et d’une ou plusieurs prestations
calculer automatiquement un total ht, TVA, ttc
définir un statut de facture
Statuts possibles :
brouillon
envoyée
payée
annulée

Génération PDF
L’utilisateur doit pouvoir :
générer une facture PDF
télécharger le document généré
Le contenu du PDF doit être construit dynamiquement à partir des données saisies dans l’application.
Le document doit au minimum contenir :
les informations du freelance
les informations du client
la date
les lignes de prestation
le total

Contraintes techniques
Stack de votre choix
Gestion de projet
Le projet doit être organisé dans GitHub Projects avec :
Penser à utiliser une organisation 
un backlog clair
des issues bien rédigées et en anglais
des tâches affectées
un suivi d’avancement visible

Git et collaboration
Vous devez utiliser une stratégie Git claire avec au minimum :
main
develop
feature/*
Les évolutions doivent passer par des Pull Requests.
Les PR doivent être compréhensibles et reliées aux issues lorsque c’est pertinent.

Qualité de code
Le projet doit inclure :
lint
Prettier
hooks pre-commit

Tests
Vous devez mettre en place :
des tests unitaires backend obligatoires
Un test de charge avec des metrics
une exécution automatique des tests dans la CI

Docker
Le projet doit être exécutable localement avec Docker.
Attendus minimum :
un Dockerfile pour le frontend
un Dockerfile pour le backend
un docker-compose.yml ou équivalent pour lancer l’ensemble

CI/CD
Vous devez mettre en place une pipeline CI qui exécute automatiquement au minimum :
installation des dépendances
lint
vérification du formatage
tests
build
build des images Docker
publication sur un registre
déploiement automatique

Déploiement
L’application doit être accessible en ligne avec une URL fonctionnelle.
Provider de votre choix (render, railway, AWS, Azure, VPS privé, Vercel etc…)
Vous devez fournir :
le lien du frontend
le lien du backend si pertinent
une explication brève de votre stratégie de déploiement

Versioning et releases
Vous devez produire :
des tags Git
une release GitHub
une version identifiable du projet, par exemple v1.0.0

Documentation
Le dépôt doit contenir un README.md expliquant clairement :
la stack choisie
l’URL de production
les membres de l’équipe

Livrables attendus
À rendre :
le lien vers le dépôt GitHub, m’inviter sur @ynovzelab

Critères d’évaluation
Application fonctionnelle 
Qualité générale du produit (front et back), cohérence du workflow, gestion correcte des clients, prestations, factures etc …
Gestion de projet
Backlog, organisation des issues, clarté du pilotage dans GitHub Projects.
Qualité de code
Lint, formatage, structure, clarté et cohérence du code.
Tests
Présence, pertinence et automatisation des tests.
Containerisation
Capacité à lancer le projet proprement dans un environnement reproductible.
CI/CD et déploiement
Pipeline fonctionnelle, build maîtrisé, mise en ligne propre.
Documentation
Documentation de l’API.

Recommandations
Ne cherchez pas à faire une application trop complexe.
Un périmètre simple, bien structuré, testé et bien déployé sera mieux évalué qu’un projet ambitieux mais instable.
Concentrez-vous sur :
la cohérence fonctionnelle
la qualité technique
la collaboration
la livraison

