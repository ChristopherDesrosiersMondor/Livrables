# Livrable 2
Pour le deuxième livrable, nous allons livrer les parties suivantes de notre application:

    1. Rafinement des APIs et documentation swagger
    2. Page connexion et création de compte pour l'application mobile avec Flutter
    3. Début du client web en Svelte
    4. Tous les micro-services sur Docker
    5. Utilisation de consul et adminer

## Rafinement des APIs et documentation swagger

## Page connexion et création de compte pour l'application mobile avec Flutter

## Début du client web en Svelte
Nous utilisons un nouveau framework pour le développement du front-end. Sveltekit est un framework qui offre une alternative à react avec des fonctionnalités intéressante pour le développement d'applications de toutes les tailles et de tous les types.

D'abord, Svelte permet aux développeurs de choisir entre le *server side rendering* et le *client side rendering*, mais également de mélanger les deux pour offrir la meilleure expérience possible pour chaque cas de figure. Ensuite, Svelte offre une approche raffraichissante au *routing* client. En effet, en svelte, il suffit de créer une arborescence de dossier dans celui nommé *routing* pour créer des routes. Ainsi, un dossier nommé *about* dans le dossier *routing* va automatiquement créer une route vers */about* lors de l'exécution du projet. Ceci permet aussi l'application simple d'un *layout* à toutes les routes imbriquées. En effet, si un fichier *+layout.svelte* se trouve dans *routing*, les styles définis dans ce fichier seront appliqués à toutes les pages qui sont plus basses dans l'arborescence à moins qu'un autre *layout* soit définit dans une route particulière. Ceci permet également de placer des composants réutilisés par toutes les pages comme la barre de navigation et la barre de bas de page par exemple. Voici un exemple de notre structure de projet pour mieux comprendre, ainsi que le contenu de chaque fichier pour mettre l'emphase sur la force du framework.

### Structure du projet
![Structure du projet](./images/svelte-project-structure.png)

## Tous les micro-services sur Docker

## Utilisation de consul et adminer