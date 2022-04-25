# Buildpack for Pnpm

Ce buildpack a été dévelopé pour déployer des applications **nodejs** utilisant le manager de package **pnpm** sur *Scalingo*.

## How it work

ajouter dans votre application Scalingo la variable d'environnement `BUILDPACK_URL`

```sh
# variable d'environnement permettant de définir le buildpack utilisé par une application Scalingo
BUILDPACK_URL=https://github.com/c0urg3tt3/pnpm-buildpack
```

au prochain déploiement de votre application, le buildpack `pnpm-buildpack` sera utilisé.


### le cycle de vie du buildpack:
- detect
  - détecte si le projet supporte pnpm via le fichier `pnpm-workspace.yaml`

- compile
  - installe `node@16`
  - installe `pnpm@6`
  - execute `pnpm build`

- release
  - execute `pnpm start`

## Node Hooks

### Build

le script `build` du fichier de configuration du projet `package.json` est exécuté lors de l'execution de la commande [buildpack/bin/compile](./bin/compile) par le buildpack.

> `pnpm build` est executé avant de construire l'image de l'application qui sera instancié sur le container.

### Start

le script `start` du fichier de configuration du projet `package.json` est exécuté lors de l'execution de la commande [buildpack/bin/release](./bin/release) par le buildpack.

> `pnpm start` est executé quand l'image de l'application est instancié sur le container.

## Development

```sh
# démarrer une instance locale d'un container Scalingo
# /path/to/custom-buildpack -> doit-être mis à jour avec le chemin de votre buildpack locale
# /path/to/application -> doit-être mis à jour avec le chemin de votre application locale
docker run --pull always --rm --interactive --tty -e STACK=scalingo-20 -v /path/to/custom-buildpack:/buildpack -v /path/to/application:/build scalingo/scalingo-20:latest bash
# créer les dossiers utilisées par le buildpack
mkdir /tmp/{cache,env}
# tester la commande de compile
/buildpack/bin/compile.sh /build /tmp/cache /tmp/env
```

> pour plus d'information, voir la documentation d'un [custom buildpack](https://doc.scalingo.com/platform/deployment/buildpacks/custom) sur Scalingo.
