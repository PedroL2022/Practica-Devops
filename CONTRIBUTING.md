# Guía de contribución

1. Haz un *fork* o crea una rama desde `main`: `git checkout -b feature/nombre-corto`.
2. Instala dependencias: `npm install`.
3. Escribe primero (o junto al cambio) la prueba en `tests/`.
4. Verifica en local: `npm run lint && npm test` (la cobertura mínima es 80 % en líneas).
5. Commits claros en español o inglés, en imperativo: `Agrega endpoint DELETE de tareas`.
6. Sube la rama y abre un *Pull Request* hacia `main`. El pipeline CI debe quedar en verde antes de fusionar.
7. Solo lo que llega a `main` se despliega a GitHub Pages.
