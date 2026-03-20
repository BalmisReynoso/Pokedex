# Pokédex Tracker - HeartGold / SoulSilver

Esta es una aplicación de seguimiento interactiva (Tracker) para Pokémon HeartGold y SoulSilver, construida con React, Vite y TailwindCSS. 
Sirve como asistente táctico y competitivo para la aventura, gestión de equipos, calculadoras de tipos, y control de captura de legendarios.

## Tecnologías Utilizadas
- **React.js**: Biblioteca principal manejando el estado (datos en `App.jsx`).
- **Vite**: Empaquetador y servidor de desarrollo.
- **Tailwind CSS v3**: Motor de estilos CSS utilitarios.
- **Lucide React**: Biblioteca de iconos SVG.

## Instalación y Uso Local

Para correr el proyecto en tu entorno local:

1. Instalar las dependencias:
   ```bash
   npm install
   ```
2. Ejecutar el servidor de desarrollo local (normalmente corre en `http://localhost:5173` o `http://localhost:5174`):
   ```bash
   npm run dev
   ```

## Despliegue Público (GitHub Pages)

El proyecto está configurado para publicarse de forma estática en **GitHub Pages**. 
Para subir una nueva versión pública haz lo siguiente:

1. Si haces cambios al código, asegúrate de guardar.
2. Ejecuta el comando de despliegue:
   ```bash
   npm run deploy
   ```
Este comando automáticamente compilará la versión óptima para producción (`dist/`) y la enviará a la rama `gh-pages` de este repositorio. **Asegúrate de que en la configuración de GitHub > Pages**, la rama fuente elegida siga siendo `gh-pages`.

El sitio en vivo debería estar visible en tu URL de GitHub Pages (ej. `https://BalmisReynoso.github.io/Pokedex/`).

## Contexto de Configuraciones Especiales
- **Modo Oscuro**: Implementado manualmente inyectando estilos globales a `.dark` en `src/index.css` y con un botón *toggle* en el encabezado de `src/App.jsx` que manipula la clase `dark` en el `<html>`. El estado de preferencia de tema se guarda en el `localStorage` (`bubloy-theme`).
- **Autenticación en Consola**: Si tienes problemas de permiso (Error 403) enviando cambios con `git push`, recuerda que Github pide que el Log In se haga usando un **Personal Access Token (PAT)** con permisos limitados (scope: `repo`) como contraseña.
- **Base de Vite**: El archivo `vite.config.js` tiene el parámetro `base: '/Pokedex/'` explícitamente definido. Sin él, la aplicación se mostrará en blanco cuando esté desplegada en GitHub Pages.

---
*Documentación autogenerada y guardada como nota persistente de versiones pasadas.*
