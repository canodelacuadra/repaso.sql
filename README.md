# repaso.sql

Práctica interactiva de consultas SQL para alumnado de Desarrollo Web con
conocimientos básicos. Es un **único archivo HTML que funciona sin internet**:
ejecuta un motor SQL real dentro del navegador (alumno escribe, ejecuta y
comprueba sus consultas contra una base de datos imaginaria de una
biblioteca).

## Qué hace

- 8 microejercicios progresivos: `SELECT`, `WHERE`, `ORDER BY`, `LIMIT`,
  `INSERT`, `UPDATE`, `DELETE` y un reto final con `JOIN`.
- Retroalimentación didáctica: resultado real de la consulta, comparación
  con lo esperado, errores SQL traducidos al español y botón de pista.
- Los ejercicios de modificación corren sobre **copias temporales** de la
  base de datos: equivocarse no rompe nada.
- Contador de progreso: cada ejercicio resuelto se marca con un ✓.

## Cómo usarla

1. Descarga `index.html` y haz doble clic. No necesita servidor ni conexión.
2. (Opción web) Súbela a GitHub Pages: Settings → Pages → Deploy from a
   branch → `main` / `(root)`.
3. Compártela con tus alumnos tal cual: un único archivo, lista para Moodle.

## Base de datos de ejemplo (Biblioteca)

`autores`, `libros`, `socios` y `prestamos`, con datos en español y el
esquema visible dentro de la aplicación.

## Añadir o modificar ejercicios

Abre `index.html` y busca el bloque `CONFIGURACIÓN DE LA ACTIVIDAD`. Cada
ejercicio es un objeto del array `EJERCICIOS`; cópialo, péguelo y cambia sus
campos. La guía completa está en [`profesores.md`](profesores.md).

```
titulo    →  nombre en la lista lateral
tipo      →  'select' | 'insert' | 'update' | 'delete'
enunciado →  lo que debe conseguir el alumno
pista     →  ayuda bajo demanda
solucion  →  SQL correcta
verificar →  (solo DML) SELECT que comprueba el estado final
filasEsperadas → (solo DML) cuántas filas debe modificar
```

## Tecnología

- [sql.js](https://sql.js.org/) (SQLite compilado a WebAssembly), embebido
  en el propio `index.html` — sin CDN ni dependencias externas.
- CSS/JS en línea: todo autocontenido.
- Motor: **SQLite**, compatible con MySQL en las sentencias básicas
  practicadas (diferencias documentadas en `profesores.md`).

## Estructura

```
index.html      → la aplicación completa (entrega a los alumnos)
profesores.md   → guía del docente: ejercicios, BD y diferencias MySQL/SQLite
```

## Licencia

MIT — ver [LICENSE](LICENSE).