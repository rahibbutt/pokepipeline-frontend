# Pokepipeline-frontend
Pokepipeline-frontend is a single-page production-ready application built with Vue.js 3 and styled using Tailwind CSS. The app fetches Pokémon data from the [pokepipeline-backend](https://github.com/rahibbutt/pokepipeline-backend) via [PokeAPI](https://pokeapi.co/) and displays it in a responsive, user-friendly interface.

The project follows semantic versioning, includes CI/CD workflows for automated builds and deployments and is available both as a static site on [GitHub Pages](https://rahibbutt.github.io/pokepipeline-frontend/) and as a [Docker-ready](https://hub.docker.com/r/rahibbutt/frontend) image for containerized environments.

## Run production-ready build locally using docker image
1. Run Docker image locally:
```
docker run -p 9000:80 rahibbutt/frontend
```
Access the local server at: [http://localhost:9000](http://localhost:9000)

## Run project locally by cloning Github repository
1. Git clone using the web URL:
```
https://github.com/rahibbutt/pokepipeline-frontend.git
```
2. Install npm dependencies:
```
npm install
```
3. Build the project:
```
npm run build
```
4. Run project locally:
```
npm run dev
```
Access the local server at: [http://localhost:9000](http://localhost:9000)

## 📦 Technologies used

* Vue.js 3 + Tailwind
* Docker
* Semantic versioning
* GitHub Actions (CI/CD)
* Axios for API communication