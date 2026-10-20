---
description: Start the TODO development environment and verify that the application responds
allowed-tools: Bash, Read
---

# Arranca el entorno de desarrollo

Levanta la aplicación de esta copia de trabajo, el checkout principal o un worktree de `bin/tree` (`trees/issue-N`), con los scripts del proyecto, `bin/setup` y `bin/dev`, y comprueba que responde. No mates procesos existentes ni elimines volúmenes.

## Puertos

Cada copia de trabajo tiene los suyos (ver `docs/procedures/development.md`, «Varios entornos a la vez»):

1. Lee los de la sesión con `printenv APP_PORT DB_PORT`.
2. Si no están, mira si la copia de trabajo tiene `.env` con `git status --short --ignored .env` (lo muestra como `!! .env`).
   - Si lo tiene, es un worktree con el entorno sin cargar: explica que hay que cargarlo con `set -a; . ./.env; set +a` antes de abrir la sesión y detente. No uses los puertos del checkout principal.
   - Si no lo tiene, es el checkout principal: la aplicación usa el `3000` y PostgreSQL, el `5432`.

Escribe los puertos tal cual en los comandos, no `$APP_PORT`: los permisos no aprueban un comando con variables.

## Pasos

1. Si `http://localhost:<puerto de la aplicación>/healthz` ya responde y `docker compose ps` muestra en marcha los servicios de esta copia de trabajo, el entorno ya está arrancado: informa y termina.
2. Ejecuta `bin/setup`. Comprueba las herramientas, instala lo que falta y deja PostgreSQL en marcha con la base de test migrada. Si falla, informa de lo que dice y detente: es un fallo del entorno.
3. Lanza `bin/dev` en segundo plano, con la herramienta Bash en segundo plano, y conserva su salida para poder diagnosticar el arranque. Usa `bin/dev --docker` si `$ARGUMENTS` contiene `--docker`. Sin buffalo, `bin/dev` pasa solo a Docker completo y lo dice.
4. Si `bin/dev` termina enseguida con un error, informa de lo que dice y detente. Si el error es que otro proceso ocupa un puerto, no lo finalices.
5. Espera hasta que `http://localhost:<puerto de la aplicación>/healthz` responda correctamente. En modo Docker completo, la primera vez se construye la imagen y tarda más.

## Resultado

Informa de:

- modo utilizado, el que dice `bin/dev`;
- servicios iniciados;
- URL de la aplicación;
- respuesta de `/healthz`;
- cualquier fallback, advertencia o proceso que deba mantenerse activo: `bin/dev` sigue en segundo plano.
