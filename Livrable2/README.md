# Livrable 2
Pour le deuxième livrable, nous allons livrer les parties suivantes de notre application:

    1. Rafinement des APIs et documentation swagger
    2. Page connexion et création de compte pour l'application mobile avec Flutter
    3. Début du client web en Svelte
    4. Tous les micro-services sur *Docker*
    5. Utilisation de consul et adminer

## Rafinement des APIs et documentation swagger

## Page connexion et création de compte pour l'application mobile avec Flutter

## Début du client web en Svelte
Nous utilisons un nouveau framework pour le développement du front-end. Sveltekit est un framework qui offre une alternative à react avec des fonctionnalités intéressante pour le développement d'applications de toutes les tailles et de tous les types.

D'abord, Svelte permet aux développeurs de choisir entre le *server side rendering* et le *client side rendering*, mais également de mélanger les deux pour offrir la meilleure expérience possible pour chaque cas de figure. Ensuite, Svelte offre une approche raffraichissante au *routing* client. En effet, en svelte, il suffit de créer une arborescence de dossier dans celui nommé *routing* pour créer des routes. Ainsi, un dossier nommé *about* dans le dossier *routing* va automatiquement créer une route vers */about* lors de l'exécution du projet. Ceci permet aussi l'application simple d'un *layout* à toutes les routes imbriquées. En effet, si un fichier *+layout.svelte* se trouve dans *routing*, les styles définis dans ce fichier seront appliqués à toutes les pages qui sont plus basses dans l'arborescence à moins qu'un autre *layout* soit définit dans une route particulière. Ceci permet également de placer des composants réutilisés par toutes les pages comme la barre de navigation et la barre de bas de page par exemple. Voici un exemple de notre structure de projet pour mieux comprendre, ainsi que le contenu de chaque fichier pour mettre l'emphase sur la force du framework.

Voir le site de SvelteKit pour plus d'informations : <https://kit.svelte.dev/>

### Structure du projet
Comme on peut le voir, ici nous avons seulement la page à la racine, mais on voit déjà l'utilisation de *+layout.svelte* qui va s'appliquer à *+page.svelte*. Le code pour chaque page de *routing* est ci-dessous.

#### Structure
![Structure du projet](./images/project-structure-svelte.png)

#### Layout
```html
<script lang="ts">
	import Header from './header.svelte';
	import Footer from './footer.svelte';

	import 'open-props/style';
	import 'open-props/normalize';
	import 'open-props/buttons';
	import 'iconify-icon';

	import '../app.css';
	import Sidebar from '../lib/sidebar.svelte';
</script>

<div class="layout">
	<div class="header">
		<Header />
	</div>

	<main>
		<div class="sidebar">
			<Sidebar />
		</div>

		<slot />
	</main>
</div>

<div class="footer">
	<Footer />
</div>

<style>
	.layout {
		height: 100%;
		display: grid;
		grid-template-rows: auto 1fr auto;
		margin-inline: auto;
		padding-inline: var(--size-0);
		background-color: #030303;
	}

	main {
		padding-block: var(--size-9);
		width: 100%;
		height: 100%;
		display: flex;
		position: fixed;
		z-index: 0;
	}

	.header {
		z-index: 1;
	}

	.sidebar {
		width: 15%;
		padding-left: 10px;
		padding-top: 20px;
		background-color: #1a1a1b;
	}

	.footer {
		text-align: center;
	}
</style>
```

#### Page
```html
<h1>Welcome to SvelteKit</h1>
<p>Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation</p>
```

#### Header
```html
<script lang="ts">
	import * as config from '$lib/config';
	import Login from '$lib/login.svelte';
	import SearchBar from '$lib/search_bar.svelte';
	import '../app.css';
</script>

<nav>
	<!-- Title -->
	<a href="/" class="title">
		<b>{config.title}</b>
		<!-- <p>{config.description}</p> -->
	</a>

	<!-- Search bar -->
	<SearchBar />

	<!-- Login -->
	<Login />
</nav>

<!-- Source: https://www.reddit.com -->

<style>
	nav {
		color: var(--text-2);
		align-items: center;
		display: inline-flex;
		flex-direction: row;
		flex-grow: 1;
		padding-block: var(--size-2);
		position: fixed;
		top: 0;
		width: 100%;
		height: 7%;
		padding-left: 1%;
		padding-right: 1%;
		border-bottom: 1px solid var(--gray-7);
	}

	a {
		color: inherit;
		text-decoration: none;
	}

	@media (min-width: 768px) {
		nav {
			display: flex;
			justify-content: space-between;
		}
	}
</style>
```

#### Footer
```html
<script lang="ts">
	import * as config from '$lib/config';
	import { CornerDownLeftIcon } from 'lucide-svelte';
	import '../app.css';
</script>

<div>
	<footer>
		<p>{config.title} &copy {new Date().getFullYear()}</p>
	</footer>
</div>

<style>
	footer {
		padding-block: var(--size-2);
		border-top: 1px solid var(--border);
	}

	p {
		color: var(--text-2);
		font-size: small;
	}
</style>
```

### L'utilisation du dossier lib
Dans svelte, les composants et les fichiers de configurations sont tous mis dans le dossier *lib* qui est ensuite accessible partout dans l'environnement en utilisant *$lib/nom_de_fichier.anything*.

#### Login
```html
<script lang="ts">
	import '../app.css';
</script>

<!-- Login -->
<div class="log-in-container">
	<a
		role="button"
		tabindex="0"
		href="https://www.reddit.com/login/?dest=https%3A%2F%2Fwww.reddit.com%2F"
		class="log-in-color">Log In</a
	>
</div>

<style>
	a:link {
		color: white;
	}

	.log-in-container {
		display: flex;
		align-items: center;
		justify-content: center;
		background-color: var(--orange-10);
		border: 1px solid transparent;
		border-radius: 1.25em;
		box-shadow: none;
		box-sizing: border-box;
		height: 36px;
		position: relative;
		min-width: 72px;
	}

	.log-in-color {
		display: flex;
		color: var(--text-1);
		text-decoration: none;
	}
</style>
```

#### Search bar
```html
<script>
	import '../app.css';
	import { Search } from 'lucide-svelte';
</script>

<div class="search-container">
	<div id="SearchDropdown" class="search-level-one rounded-box-container rounded-box">
		<div class="search-level-two">
			<form action="" method="get" role="search" class="search-form">
				<label for="header-search-bar" class="label-search">
					<div class="icon-container">
						<Search />
					</div>
				</label>
				<input
					type="search"
					class="header-search-input"
					id="header-search-bar"
					placeholder="Search Hublot"
					value
				/>
			</form>
		</div>
	</div>
</div>

<!-- Source: https://www.reddit.com -->

<style>
	.search-container {
		fill: aqua;
		flex-grow: 1;
		margin: 0 auto;
		max-width: 690px;
		padding-bottom: 6px;
	}

	.search-level-one {
		fill: aqua;
		-ms-flex-positive: 1;
		flex-grow: 1;
		margin-left: 16px;
		margin-right: 16px;
		width: auto;
		height: auto;
	}

	.rounded-box-container {
		border: 1px solid transparent;
		border-radius: 4px;
		box-sizing: border-box;
		height: 36px;
		position: relative;
		min-width: 72px;
	}

	.search-level-two {
		-ms-flex-align: center;
		align-items: center;
		box-sizing: border-box;
		background-color: var(--gray-9);
		border: 1px solid var(--gray-7);
		border-radius: 1.25em;
		box-shadow: none;
		height: 40px;
	}

	.search-form {
		width: 100%;
		height: 100%;
		display: flex;
	}

	.label-search {
		display: flex;
		cursor: default;
	}

	.header-search-input {
		appearance: none;
		color: var(--text-1);
		font-size: 14px;
		line-height: 14px;
		margin-right: 16px;
		outline: none;
		width: 100%;
		background-color: var(--gray-9);
	}

	.search-level-two:hover,
	.header-search-input:hover {
		background-color: var(--gray-11);
	}

	.search-level-two:hover {
		border: 1px solid var(--gray-5);
	}

	.icon-container {
		display: -ms-flexbox;
		display: flex;
		-ms-flex-align: center;
		align-items: center;
		padding: 0 9px 0 15px;
	}
</style>
```

#### Side bar
```html
<script>
	import { Home } from 'lucide-svelte';
	import '../app.css';
</script>

<!-- Side navigation -->

<!-- svelte-ignore a11y-missing-attribute -->
<p class="title">FEEDS</p>
<br />
<!-- svelte-ignore a11y-missing-attribute -->
<p class="categ">Home</p>
<!-- svelte-ignore a11y-missing-attribute -->
<p class="categ">Popular</p>
<br />

<!-- Source: https://www.w3schools.com/howto/howto_css_fixed_sidebar.asp -->

<style>
	.title {
		color: var(--text-2);
		font-size: small;
	}

	.categ {
		font-size: medium;
	}
</style>
```

### Utilisation de d'autres fonctionnalités de *Svelte*
Ici nous allons montrer un autre projet fait par un membre de l'équipe pour démontrer des outils utiles qui seront surement utilisés plus tard dans notre projet et pour démontrer notre compréhension des fonctionnalités de Svelte. Si vous pensez à la modification d'une variable dans un projet react par exemple, vous penserez surement à la fonctionalité *useState/setState*. Comme vous verrez dans l'exemple suivant, Svelte nous donne la possibilité d'utiliser directement la variable déclarée en javascript dans une balise html avec la propriété *bind*.

```html
<script lang="ts">
	let nbPersonnes: number = 0;
	let prixTotal: number;
	let sousTotal: number;
	let taxes: number;
	let pourboire: number;
	let taxesLivraison: number;
	let fraisLivraison: number;
	let fraisServices: number;
	let modePaiement: string = '';

	let name: Text;
	let price: number;

	let totalTest: number = 0;
	let result: string = '';

	let commandes = new Map();
	let our_shares = new Map();

	function total() {
		totalTest = 0;
		commandes.forEach((value: number, key: string) => {
			totalTest += value;
		});
	}

	function addSomeone() {
		commandes.set(name, price);
		commandes = commandes;
		nbPersonnes += 1;
		total();
	}

	function calculer() {
		result = '';
		console.log(
			taxesLivraison + '' + fraisLivraison + '' + fraisServices + '' + nbPersonnes + '' + sousTotal
		);
		let equalShareExtras =
			(taxesLivraison + fraisLivraison + fraisServices + pourboire) / nbPersonnes;
		commandes.forEach((value: number, key: string) => {
			let percentage_bill = value / sousTotal;
			let percentage_taxes = percentage_bill * taxes;
			let debt = value + percentage_taxes + equalShareExtras;
			our_shares.set(key, debt);
		});
		our_shares = our_shares;
	}
</script>

<h1>This playgroup pays it's debts!</h1>
<p>Hey la gang, hope the food was good!</p>
<br />

<input type="number" placeholder="Prix total" bind:value={prixTotal} /><br />
<input type="number" placeholder="Sous-total" bind:value={sousTotal} /><br />
<input type="number" placeholder="Taxes" bind:value={taxes} /><br />
<input type="number" placeholder="Taxes de livraison" bind:value={taxesLivraison} /><br />
<input type="number" placeholder="Frais de livraison" bind:value={fraisLivraison} /><br />
<input type="number" placeholder="Frais de service" bind:value={fraisServices} /><br />
<input type="number" placeholder="Pourboire" bind:value={pourboire} /><br />
<input type="Text" placeholder="Courriel ou tel." bind:value={modePaiement} /><br />
<br />
{#each [...commandes] as [key, value]}
	<div>{key} - {value}</div>
{/each}
<br />

<input type="Text" placeholder="Nom" bind:value={name} />
<input type="number" placeholder="Prix de la commande" bind:value={price} />
<button on:click={addSomeone}>Ajouter</button> <br />

<button on:click={calculer}>Calculer</button>

<br />

<h2>Nos dettes</h2>
{#each [...our_shares] as [key, value]}
	<p>{key} - {value.toFixed(2)} $</p>
{/each}

{#if modePaiement != ''}
	<p>Payer au: {modePaiement}</p>
{/if}
```

## Tous les micro-services sur *Docker*
Dans cette section nous allons présenter notre architecture *docker* avec notre fichier *docker* compose, notre registre de conteneur *docker hub* ainsi que notre environnement *Docker desktop*.

### Compose
```yaml
# Use postgres/example user/password credentials
version: "3.1"

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
    image: darkseacollective/ms_post:version1.0
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

### Registre Docker hub
Ce registre public permet à tout le monde d'utiliser notre *Docker compose file* pour déployer notre *backend* sur leur machine en assumant qu'iels ont *docker* et *docker compose* d'installer sur leurs machines.

![Registre docker hub](./images/docker-hub-registry.png)

En utilisant simplement le nom de notre registre et le nom de l'image désirée, vous pouvez utiliser les images individuelles dans vos propres projets *Docker*.

### Environnement Docker Desktop
L'environnement graphique *Docker* facilite grandement le développement de notre projet.

![Docker Desktop](./images/docker-desktop-environnement.png)

## Utilisation de consul et adminer
### Consul
*Consul* est un service permettant de garder un registre de nos services, comme *eureka* qui avait été présenté en classe. L'avantage de *Consul* c'est qu'il offre la possibilité de développer le service sous *Kubernetes* pour offrir une *api gateway* basée sur les informations du registres. Nous allons tenter de déployer le projet sous cette forme pour le livrable 3.

![Consul registry](./images/consul-registry-dashboard.png)

### Adminer
*Adminer* a remplacé *pgadmin* dans notre architecture parce que ce dernier avait des difficultés au niveau du déploiement en docker à cause de problèmes de droits d'accès réseau. L'interface de ce nouvel outil est plus simple, mais toute aussi puissante pour les besoin de notre déploiement. On a accès à des outils de gestion et nous pouvons facilement voir les données stockées dans notre base de données lors de l'exécution de nos services. 

#### Adminer page d'accueil

![Adminer login](./images/adminer-login-page.png)

#### Adminer database selector

![Adminer databse selctor](./images/adminer-database-selector.png)

#### Adminer database dashboard

![Adminer database dashboard](./images/adminer-database-dashboard.png)

#### Adminer data viewer

![Adminer data viewer](./images/adminer-data-viewer.png)