# Livrable 3
Pour le troisième livrable, nous allons livrer les parties suivantes de notre application:

    1. Client web utilisant les fonctionnalités de chaque microservices
    2. Client mobile utilisant les fonctionnalités de chaque microservices
    3. Une version docker des microservices pour un déploiement simple
    4. Une documentation complète pour le projet
    5. Un guide utilisateur.ice et un guide de développement sous format vidéo

## Client Web
Pour l'application web, nous avons étoffé l'interface commencée dans le livrable 2.

Lors du lancement de l'application, nous arrivons sur une page d'accueil qui contient les éléments suivants :

 - un header : logo, nom du site, barre de recherche, bouton Log in pour se connecter, icones soleil/lune pour régler le thème du site a dark ou light
 - une sidebar à gauche : lien vers l'accueil (Home), liens vers différentes commuunautés, bouton Sign inc pour se créer un compte
 - un thread central contenant les posts présents dans la base de données

L'utilisateur a la possibilité de naviguer sur le site sans se connecter et accéder à toutes les communautés et tous les posts. Néanmoins, s'il souhaite créer un post ou un communauté, il est nécessaire de se créer un compte.

Le bouton Sign in dans la sidebar ouvre une petite fenêtre avec un formulaire pour recueillir les informations du nouvel utilisateur (nom, prénom, date de naissance, pseudo, mot de passe, etc). Une fois le compte créé, l'utilisateur retrouve la page d'accueil modifiée :

- le pseudo est affiché dans le header
- le bouton Sign in n'apparaît plus
- le bouton Log in est remplacé par un bouton Log out pour se déconnecter
- un encart apparaît à droite du thread de posts avec un petit mot de bienvenue et 2 boutons pour créer un post et créer une communauté.

## Client Mobile
Pour l'application mobile, nous avons ajouté les pages suivantes: la page *Home* et la page création de post. La page Home affiche tous les posts de la base de données. Tous les posts contiennent les éléments suivants: icon(account), nom de l'utilisateur ayant créé la publication, la date de création, un icon (3 points) permettant de supprimer un post, le titre, le contenu(s'il y a lieu), l'image(s'il y a lieu) ainsi que les boutons(icon) *upvote* et *downvote* tous les deux fonctionnels. Les autres boutons sont présents mais non fonctionnels: *comment* et *share*. 

Ensuite, la page *Create post* est divisé en deux. Une première page contenant des champs *input* permettant d'entrer le titre, le contenu et le lien de l'image désiré et une deuxième page pour choisir la communauté où publier. 

Important: la création de publication peut se faire seulement si l'utilisateur est connecté! Une autre fonctionnalité que nous avons ajouté. Pour le moment, il faut se connecter à chaque création de publication, car l'application ne garde pas en mémoire l'utilisateur connecté après qu'une publication soit créée (problème à régler). Par contre, la publication se créée efficament selon l'utilisateur qui vient de se connecter.

Finalement, l'application contient une barre de navigation au bas de la page. Le premier icon renvoie à la page *Home* (fonctionnelle), le deuxième à *Discovery* (en construction), le troisième à la page *Create post* (fonctionnelle), le quatrième à la page *Chat* (en construction) et le dernier à la page *Inbox* (en construction). Le haut de la page contient divers éléments aussi (afin d'imiter l'application Reddit): un icon menu(non fonctionnel), un *dropdown menu* (non fonctionnel), un icon recherche (non fonctionnel) et un icon *account* lui redirigeant vers la page de connexion. À noter que la page de connection permet d'accéder à la page de création de compte. 

#### Sceenshots
##### Page Connexion
En premier l'utilisateur se connecte
![Page Connexion](./images/loginpage.png)
##### Page Create post
Après l'utilisateur insère le titre, le contenu et le lien de l'image du post à créer
![Page CreatePost - 1](./images/createpostpage1.png)
Il choisit la communauté désirée
![Page CreatePost - 2](./images/createpostpage2.png)
Il publie le tout!
![Page CreatePost - 3](./images/createpostpage3.png)
##### Page Home
![Page Home](./images/home.png)




## Images docker
### Microservices

### Client Web

## Documentation complète pour le projet
### Microservices
Nous avons développer les trois microservices: ms_community, ms_account, ms_post avec l'aide de java springboot et de maven. Nous avons pris avantage du pouvoirs des annotations Lombok et de plusieurs dépendences web et base de données qui s'intégraient dans un projet java backend comme le notre. Pour reproduire nos microservices il vous faudra lancer un projet avec springboot initializer <https://start.spring.io/> en sélectionnant les options suivantes : Maven, Java, Spring boot 3.1.0 Java 17 et Packaging .jar. Ensuite, pour éviter tout type d'erreur, je vous recommande de prendre ce document pom.xml et de remplacer le contenu généré par initializer par celui-ci:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>3.0.6</version>
		<relativePath /> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.example</groupId>
	<artifactId>ms_community</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>ms_community</name>
	<description>Demo project for Spring Boot</description>
	<properties>
		<java.version>17</java.version>
		<spring-cloud.version>2022.0.1</spring-cloud.version>
	</properties>
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.hibernate.validator</groupId>
			<artifactId>hibernate-validator</artifactId>
		</dependency>

		<dependency>
			<groupId>org.postgresql</groupId>
			<artifactId>postgresql</artifactId>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<optional>true</optional>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>

		<dependency>
			<groupId>org.springdoc</groupId>
			<artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
			<version>2.1.0</version>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-consul-discovery</artifactId>
		</dependency>
	</dependencies>
	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>${spring-cloud.version}</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
				<configuration>
					<excludes>
						<exclude>
							<groupId>org.projectlombok</groupId>
							<artifactId>lombok</artifactId>
						</exclude>
					</excludes>
				</configuration>
			</plugin>

			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-surefire-plugin</artifactId>
				<version>2.19.1</version>
				<configuration>
					<testFailureIgnore>true</testFailureIgnore>
				</configuration>
			</plugin>
		</plugins>
	</build>

</project>
```
Assurez vous que le group id <groupId>com.example</groupId> représente les données que vous avez entrées lors de la création du projet.

Les prochaines étapes sont les mêmes pour chaque microservices, avec seul changement le contenu du modèle et du controlleur pour représenter les objets spécifiques à votre projet.

Dans le dossier contenant votre fichier application, créer un dossier controller, model, repository. Dans chacun, créer un fichier nommé XController, X, XRepository respectivement ou X est l'objet représenter par votre microservice, dans notre cas CommunityController par exemple. Votre arbre de projet devrait ressembler à ça pour le dossier src:

src
--main
----java
------com
--------exemple
----------ms_community(nom de votre projet)
------------controller
--------------CommunityController.java
------------model
--------------Community.java
------------repository
--------------CommunityRepository.java
------------MsCommunityApplication.java

Nous allons maintenant donner un exemple du code de chacun de ces 4 fichiers et expliquer les annotations et utilisations des technologies de développement java dans le cadre de notre projet pour faciliter votre compréhension et votre capacité à reproduire cette partie du projet. L'explication sera fait dans les commentaires du code et annoncée par une ligne comme celle-ci:
// *******************************

#### Code
##### Controller
```java
package com.example.ms_community.controller;

import java.util.ArrayList;
import java.util.List;
import java.util.Optional;

import io.swagger.v3.oas.annotations.Operation;
import io.swagger.v3.oas.annotations.media.Content;
import io.swagger.v3.oas.annotations.media.Schema;
import io.swagger.v3.oas.annotations.responses.ApiResponse;
import io.swagger.v3.oas.annotations.responses.ApiResponses;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.CrossOrigin;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import com.example.ms_community.repository.CommunityRepository;
import com.example.ms_community.model.Community;;
// *******************************
// Cross origin est une annotation fournie par la dépendance springframework web nous permettant de facilement gérer les demandes venant d'autres domaines vers notre microservice. Ici les paramètres sont ignorés pour prendre le comportement par défaut. RestController dit au projet et aux autres outils que cette class est un controlleur d'une api Rest. RequestMapping indique que la route de base pour ce api endpoint est /communities, les CRUD mapping qui suivent dans le code sont donc ajouté à ce chemin, par exemple le PostMapping plus bas sera accessible à /communities/add
@CrossOrigin
@RestController
@RequestMapping("/communities")
public class CommunityController {
    // *******************************
    // Autowired permet de trouver automatiquement le repository dans le projet
    @Autowired
    private CommunityRepository communityRepository;
    
    // *******************************
    // Les annotations Operation et ApiResponses viennent de la dépendance swagger ajoutée à notre projet dans le pom.xml mentionné plus haut. Ces annotations seront interprétées par swagger pour offrir de l'information sur l'api. Cette information peut être visualisé au localhost:(port défini dans application.properties pour votre projet java, ici 8081)/swagger-ui/index.html
    @Operation(summary = "Adds a community do the database")
    @ApiResponses(value = {
            @ApiResponse(responseCode = "201", description = "Community created", content = {
                    @Content(mediaType = "application/json", schema = @Schema(implementation = Community.class)) }),
            @ApiResponse(responseCode = "400", description = "Invalid data", content = @Content),
            @ApiResponse(responseCode = "500", description = "Internal server error", content = @Content)
    })
    @PostMapping("/add")
    public ResponseEntity<Community> addNewCommunity(@RequestBody Community newCommunity) {
        try {
            Community communityToAdd = communityRepository.save(new Community(newCommunity));
            return new ResponseEntity<>(communityToAdd, HttpStatus.CREATED);
        } catch (Exception e) {
            return new ResponseEntity<>(null, HttpStatus.INTERNAL_SERVER_ERROR);
        }
    }

    @Operation(summary = "Gets all communities")
    @ApiResponses(value = {
            @ApiResponse(responseCode = "200", description = "Found communities", content = {
                    @Content(mediaType = "application/json", schema = @Schema(implementation = Community.class)) }),
            @ApiResponse(responseCode = "404", description = "The communities were not found", content = @Content),
            @ApiResponse(responseCode = "500", description = "Internal server error", content = @Content)
    })
    @GetMapping("/view/all")
    public ResponseEntity<List<Community>> getAllCommunities() {
        try {
            List<Community> CommunityList = new ArrayList<Community>();

            communityRepository.findAll().forEach(CommunityList::add);

            if (CommunityList.isEmpty()) {
                return new ResponseEntity<>(HttpStatus.NO_CONTENT);
            }

            return new ResponseEntity<>(CommunityList, HttpStatus.OK);
        } catch (Exception e) {
            return new ResponseEntity<>(null, HttpStatus.INTERNAL_SERVER_ERROR);
        }
    }

    @Operation(summary = "Get a community by its id")
    @ApiResponses(value = {
            @ApiResponse(responseCode = "200", description = "Found the community", content = {
                    @Content(mediaType = "application/json", schema = @Schema(implementation = Community.class)) }),
            @ApiResponse(responseCode = "400", description = "Invalid id supplied", content = @Content),
            @ApiResponse(responseCode = "404", description = "The community was not found", content = @Content),
            @ApiResponse(responseCode = "500", description = "Internal server error", content = @Content)
    })
    @GetMapping("/view/{id}")
    public ResponseEntity<Community> getCommunityById(@PathVariable("id") long id) {
        Optional<Community> CommunityData = communityRepository.findById(id);

        if (CommunityData.isPresent()) {
            return new ResponseEntity<>(CommunityData.get(), HttpStatus.OK);
        } else {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
    }

    @Operation(summary = "Edits a community by its id")
    @ApiResponses(value = {
            @ApiResponse(responseCode = "200", description = "Community updated", content = {
                    @Content(mediaType = "application/json", schema = @Schema(implementation = Community.class)) }),
            @ApiResponse(responseCode = "400", description = "Invalid id supplied", content = @Content),
            @ApiResponse(responseCode = "404", description = "The community was not found", content = @Content),
            @ApiResponse(responseCode = "500", description = "Internal server error", content = @Content)
    })
    @PutMapping("/edit/{id}")
    public ResponseEntity<Community> updateCommunity(@PathVariable("id") long id,
            @RequestBody Community updateCommunity) {
        Optional<Community> communityData = communityRepository.findById(id);

        if (communityData.isPresent()) {
            Community modifiedCommunity = communityData.get();
            modifiedCommunity = new Community(updateCommunity);
            return new ResponseEntity<>(communityRepository.save(modifiedCommunity), HttpStatus.OK);
        } else {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
    }

    @Operation(summary = "Deletes a community by its id")
    @ApiResponses(value = {
            @ApiResponse(responseCode = "200", description = "Community deleted", content = {
                    @Content(mediaType = "application/json", schema = @Schema(implementation = Community.class)) }),
            @ApiResponse(responseCode = "400", description = "Invalid id supplied", content = @Content),
            @ApiResponse(responseCode = "404", description = "The community was not found", content = @Content),
            @ApiResponse(responseCode = "500", description = "Internal server error", content = @Content)
    })
    @DeleteMapping("/delete/{id}")
    public ResponseEntity<Community> deleteCommunity(@PathVariable("id") long id) {
        Optional<Community> communityData = communityRepository.findById(id);
        if (communityData.isPresent()) {
            try {
                Community deletedCommunity = communityData.get();
                communityRepository.deleteById(id);
                return new ResponseEntity<>(deletedCommunity, HttpStatus.OK);
            } catch (Exception e) {
                return new ResponseEntity<>(HttpStatus.INTERNAL_SERVER_ERROR);
            }
        } else {
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }
    }
}
```

##### Model
```java
package com.example.ms_community.model;

import java.sql.Date;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;

// ******************************
// Les annotations Table et Entity font le travailler de communiquer l'information à la base de donnée sur le fait que cette classe doit être une entité pour java et une table pour postgres, permettant de manipuler les informations du côté code et du côté base de donnée avec une approche code first.
@Table(name = "community")
@Entity
// ******************************
// Les annotations Getter et Setter sont utilisées pour diminuer le code nécessaire pour intéragir avec les propriétés du model dans d'autres parties de votre application.
@Getter
@Setter
public class Community {
    // ******************************
    // Les annotations Id, GeneratedValue et Column permettent comme Table de communiqué des informations à la base de données sur le rôles des paramètres de cette classe et aussi de comprendre automatiquement leur types et leurs assignations. 
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Integer id;

    @Column(name = "Community name")
    private String communityName;

    @Column(name = "Description")
    private String communityDescription;

    @Column(name = "Community logo")
    private String communityLogo;

    @Column(name = "Community header image")
    private String communityHeaderImage;

    @Column(name = "Created on")
    private Date communityCreatedOnDate;

    @Column(name = "Ammount of members")
    private Integer communityAmmountOfMembers;

    @Column(name = "Ammount of posts")
    private Integer communityAmmountOfPosts;

    @Column(name = "Community creator id")
    private Integer communityCreatorId;

    // ******************************
    // Les constructeurs permettent à notre controlleur de créer une instance du modèle à partir d'une entité au lieu de le faire a la main a chaque fois pour avoir les informations pour tous les champs.

    public Community() {

    }

    public Community(Community newCommunity) {
        this.communityName = newCommunity.getCommunityName();
        this.communityDescription = newCommunity.getCommunityDescription();
        this.communityCreatedOnDate = newCommunity.getCommunityCreatedOnDate();
        this.communityCreatorId = newCommunity.getCommunityCreatorId();
        this.communityLogo = newCommunity.getCommunityLogo();
        this.communityHeaderImage = newCommunity.getCommunityHeaderImage();
        this.communityAmmountOfMembers = newCommunity.getCommunityAmmountOfMembers();
        this.communityAmmountOfPosts = newCommunity.getCommunityAmmountOfPosts();
    }
}
```

##### Repository
Le repository est un outil qui permet à l'ORM offert par java de connecter nos entités à la base de donnée en utilisant une approche code first. En créant une interface qui extend CrudRepository nous permettons à notre application de faire les opérations crud sur nos entités et de voir les changements automatiquement représentés dans la base de données.
```java
package com.example.ms_community.repository;

import org.springframework.data.repository.CrudRepository;
import com.example.ms_community.model.Community;;

public interface CommunityRepository extends CrudRepository<Community, Long>{}
```

##### Application
Nous incluons le code du fichier d'application pour vous permettre de voir si le votre est semblable et aider dans le debuggage si jamais vous voyez des problèmes.
```java
package com.example.ms_community;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MsCommunityApplication {

	public static void main(String[] args) {
		SpringApplication.run(MsCommunityApplication.class, args);
	}

}
```

##### application.properties
Dans ce fichier, il est important de comprendre plusieurs choses.

spring.application.name permet de reconnaitre l'application lors de l'exécution.

server.port peut être changer pour n'importe quel port qui n'est pas utilisé, mais il faudra faire attention de le changer plus loin dans la configuration du Dockerfile et de docker compose qui seront expliqués plus tard.

Les propriétés url, username et password sont les valeur de votre base de données.

Le bloc de propriétés jpa peut être copié, il permet à l'application de communiquer correctement avec la base de données.

Les propriétés de consul sont reliés à la configuration de consul dans le docker compose. Ici il faut mettre le nom du container donné à consul dans .host, le port dans .port et mettre true pour la dernière propriété pour permettre à ce micro-service d'être découvert par consul lors du déploiement sur docker.
```properties
spring.application.name=ms_community

server.port=8081

spring.datasource.url=jdbc:postgresql://localhost:5430/hublot.hull
spring.datasource.username=darksea
spring.datasource.password=root

spring.jpa.hibernate.ddl-auto=update
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.PostgreSQLDialect
spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true

spring.cloud.consul.host=consul-container
spring.cloud.consul.port=8500
spring.cloud.consul.discovery.prefer-ip-address=true
```

##### DockerFile
La dernière étape pour pouvoir exporter votre micro-service sous le format docker est de faire un dockerfile. Voici le code.

On construit l'image à partir (FROM) une image qui utilise déjà java 17 pour être compatible avec notre projet, on crée un volume tmp qui est nécessaire pour l'exécution de l'application jar et qui nous permettra de débugger lors de l'exécution du conteneur si besoin. On COPY le .jar de notre application à la racine de l'image et on spécifie que le point d'entrée lors de l'exécution de cette image est le .jar.


```docker
FROM eclipse-temurin:17-jdk-alpine
VOLUME /tmp
COPY target/*.jar app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
```

#### Exporter le tout vers votre repo dockerhub pour utilisation ultérieure

Placez vous à la racine de votre microservice et faites les commandes suivantes pour générer un nouveau .jar avec toutes vos modifications, construire l'image docker et la push vers votre registry en ligne.

```bash
mvn clean install

docker build -t registryname(ici darkseacollective)/app_imagenametage(ici ms_community:version1.1) .

docker push registryname(ici darkseacollective)/app_imagenametage(ici ms_community:version1.1)
```

#### Docker compose
Pour compléter le backend, refaites les étapes de la dernière section pour chacun de vos microservices et revenez ici pour construire le fichier docker-compose.yaml qui vous permettra de lancer le backend en une commande. Nous allons encore utiliser les commentaires et la rangée d'étoile pour montrer ou sont les commentaires utiles à cette documentation.

```yaml
# Use postgres/example user/password credentials
version: "3.1"

# ***************************
# Les services dans un docker-compose sont toutes les images que vous voulez lancer dans le cadre du déploiement de votre conteneur. Ici le service db est la base de donnée postgres, adminer est un outil de gestion de bases de données qui permet de voir et de modifier graphiquement les informations de la base de données, consul-container sera l'image de consul qui servira de registre pour les micro-services et ensuite chaque ms_ service sera les microservices. Pour l'explication des paramètres, référez vous à la section docker du guide de développement vidéo.
services:
  db:
    image: postgres
    container_name: db
    restart: always
    environment:
      POSTGRES_USER: darksea
      POSTGRES_PASSWORD: root
      POSTGRES_DB: hublot.hull
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U $$POSTGRES_USER -d $$POSTGRES_DB"]
      interval: 1s
    expose:
      - "5432"
    ports:
      - "5432:5432"
    networks:
      - darksea
    volumes:
      - data:/var/lib/postgresql/data

  adminer:
    image: adminer:4.8.1
    container_name: adminer
    restart: always
    ports:
      - 8080:8080
    networks:
      - darksea

  consul-container:
    container_name: consul-container
    hostname: "consul-container"
    image: consul
    command: agent -server -ui -node=server1 -bootstrap-expect=1 -client=0.0.0.0
    environment:
      - CONSUL_BIND_INTERFACE=eth0
    ports:
      - "8500:8500"
      - "8600:8600/udp"
      - "8600:8600/tcp"
    networks:
      - darksea

  ms_community:
    image: darkseacollective/ms_community:version1.1
    container_name: ms_community
    ports:
      - "8081:8081"
    networks:
      - darksea
    environment:
      - SPRING_DATASOURCE_URL=jdbc:postgresql://db:5432/hublot.hull
      - SPRING_DATASOURCE_USERNAME=darksea
      - SPRING_DATASOURCE_PASSWORD=root
      - SPRING_JPA_HIBERNATE_DDL_AUTO=update
      - WAIT_FOR_HOSTS=consul-container:8500
    depends_on:
      db:
        condition: service_healthy
      consul-container:
        condition: service_started

  ms_account:
    image: darkseacollective/ms_account:version1.2
    container_name: ms_account
    ports:
      - "8082:8082"
    networks:
      - darksea
    environment:
      - SPRING_DATASOURCE_URL=jdbc:postgresql://db:5432/hublot.hull
      - SPRING_DATASOURCE_USERNAME=darksea
      - SPRING_DATASOURCE_PASSWORD=root
      - SPRING_JPA_HIBERNATE_DDL_AUTO=update
      - WAIT_FOR_HOSTS=consul-container:8500
    depends_on:
      db:
        condition: service_healthy
      consul-container:
        condition: service_started

  ms_post:
    image: darkseacollective/ms_post:version1.1
    container_name: ms_post
    ports:
      - "8083:8083"
    networks:
      - darksea
    environment:
      - SPRING_DATASOURCE_URL=jdbc:postgresql://db:5432/hublot.hull
      - SPRING_DATASOURCE_USERNAME=darksea
      - SPRING_DATASOURCE_PASSWORD=root
      - SPRING_JPA_HIBERNATE_DDL_AUTO=update
      - WAIT_FOR_HOSTS=consul-container:8500
    depends_on:
      db:
        condition: service_healthy
      consul-container:
        condition: service_started

networks:
  darksea:
    driver: bridge

volumes:
  data:
```

### Client Mobile

### Client Web
#### Svelte


#### SvelteKit

## Guides
### Guide d'utilisateur.ice

### Guide de développement
