# repaso.sql · Documento del profesor

**Público:** alumnado de Desarrollo Web con conocimientos básicos
**Nivel:** básico
**Nº de microejercicios:** 21, en dos series (8 básicos + 13 de refuerzo)
**Puntuación:** 10 puntos por ejercicio (total opcional: 210)

> Este documento te explica cómo funciona la aplicación, cómo añadir o
> modificar ejercicios y qué diferencias hay entre el motor embebido
> (SQLite) y MySQL.

---

## 1. ¿Qué es la aplicación?

`index.html` es un archivo único, autónomo y que funciona **sin internet**
(haz doble clic sobre él y todo se abre en el navegador). No necesita
servidor, ni instalar nada, ni conexión a la base de datos.

Incluye un motor SQL real (SQLite compilado a WebAssembly) embebido en el
propio archivo, junto con una base de datos de ejemplo llamada **Biblioteca**
con 4 tablas:

| Tabla      | Columnas                                              |
|------------|-------------------------------------------------------|
| `autores`  | id, nombre, pais                                      |
| `libros`   | id, titulo, id_autor, anio, genero, disponible        |
| `socios`   | id, nombre, fecha_alta                                |
| `prestamos`| id, id_libro, id_socio, fecha_prestamo                |

Los estudiantes escriben una consulta SQL, la ejecutan para ver el resultado
real y pulsan "Comprobar" para que el sistema la compare con la solución
esperada y les dé una retroalimentación didáctica (filas/columnas esperadas,
errores traducidos, pistas, etc.).

### Seguridad para la práctica
Los ejercicios de modificación (`INSERT` / `UPDATE` / `DELETE`) se ejecutan
siempre sobre una **copia temporal** de la base de datos. Cada vez que el
alumno comprueba, la base de datos se reconstruye desde cero. Es imposible
"romper" los datos por mucho que se equivoquen.

---

## 1.b. Las dos series de ejercicios

La actividad se puede usar completa o por partes: la **serie 1** (ejercicios
1-8) es la ronda inicial y la **serie 2** (9-21) es una ampliación sobre la
misma base de datos, pensada para una segunda sesión de práctica.

| # | Título | Qué practica | Tipo |
|---|--------|--------------|------|
| 1-8 | Serie 1 (ver `EJERCICIOS` en `index.html`) | `SELECT *`, columnas + `WHERE`, `WHERE` + `ORDER BY`, `ORDER BY DESC` + `LIMIT`, `INSERT`, `UPDATE`, `DELETE`, `JOIN` de dos tablas | mixta |
| 9 | AND: dos condiciones | `WHERE ... AND ...` | select |
| 10 | OR: dos opciones | `WHERE ... OR ...` | select |
| 11 | Rangos con BETWEEN | `BETWEEN` + `ORDER BY` | select |
| 12 | Buscar texto con LIKE | comodín `%` | select |
| 13 | Valores sin repetir | `DISTINCT` | select |
| 14 | Contar con COUNT | `COUNT(*)` | select |
| 15 | Agrupar con GROUP BY | `GROUP BY` + `COUNT(*)` | select |
| 16 | Reto: unir tres tablas | `JOIN` encadenado (3 tablas) | select |
| 17 | LEFT JOIN: sin préstamos | `LEFT JOIN` + `IS NULL` | select |
| 18 | Actualizar varios libros | `UPDATE` con `<>` (4 filas) | update |
| 19 | Borrar por fecha | `DELETE` comparando fechas | delete |
| 20 | Insertar varios libros | `INSERT` con varios `VALUES` | insert |
| 21 | Reto final: los 2 más recientes | `JOIN` + `WHERE` + `ORDER BY DESC` + `LIMIT` | select |

> Nota para el docente: en los ejercicios 14 y 15 el enunciado pide
> explícitamente `COUNT(*)` porque la app compara también el **nombre** de
> las columnas devueltas, y `COUNT(id)` produciría otro nombre.

---

## 2. Cómo entregarla a los alumnos

### Opción A · Descarga local (Moodle, email, carpeta)
Sube **solo el archivo `index.html`**. Cada alumno lo descarga, hace doble
clic y funciona.

### Opción B · Publicada en la web (GitHub Pages)
Sube el repositorio a GitHub y activa Pages:

- Settings → Pages → Build and deployment → Source: "Deploy from a branch" →
  rama `main` → carteta `/ (root)` → Guardar.
- La app quedará en `https://<usuario>.github.io/repaso.sql/`.

Como la app es un único archivo, la misma versión funciona por doble clic y
desde la web sin hacer nada más.

---

## 3. Cómo añadir o modificar ejercicios

Abre `index.html` con cualquier editor (VS Code, Notepad++…). El apartado a
editar es el bloque marcado como:

```
/* =====================================================================
   CONFIGURACIÓN DE LA ACTIVIDAD
   ...
   ===================================================================== */
```

### 3.1. Estructura de un ejercicio

Cada ejercicio es un objeto dentro del array `EJERCICIOS`:

```js
{
  titulo: 'Título corto',
  tipo: 'select',                 // 'select' | 'insert' | 'update' | 'delete'
  enunciado: 'Texto del enunciado que ve el alumno.',
  pista: 'Pista que se muestra al pulsar el botón Pista.',
  solucion: "SELECT ...;",        // solución correcta
  ordenSensible: false,           // true si exiges el mismo orden de filas
  verificar: "SELECT ...;",       // SOLO para insert/update/delete
  filasEsperadas: 1               // SOLO para insert/update/delete
}
```

| Campo | Para qué sirve |
|-------|----------------|
| `titulo` | Nombre que aparece en la lista lateral. |
| `tipo` | `select` evalúa el resultado de la consulta. Los tipos `insert`, `update`, `delete` evalúan cuántas filas se modifican y el estado final de los datos. |
| `enunciado` | Lo que el alumno tiene que conseguir (escrito en español, pensando en quien empieza). |
| `pista` | Ayuda opcional mostrada bajo demanda. |
| `solucion` | La sentencia SQL correcta. Sirve para generar el resultado esperado. |
| `ordenSensible` | `false` compara las filas sin importar el orden; `true` exige el mismo orden (útil cuando el ejercicio pide `ORDER BY`). |
| `verificar` | Solo en DML: una consulta `SELECT` que comprueba el estado de los datos después de tu `solucion`. Se compara "resultado del alumno" vs "resultado de la solución". |
| `filasEsperadas` | Solo en DML: cuántas filas debe modificar la sentencia. Detecta, por ejemplo, un `UPDATE` o `DELETE` sin `WHERE` (que modificaría todas las filas). |

### 3.2. Añadir un ejercicio nuevo

1. Copia la línea `},` completa de un ejercicio existente.
2. Pégalo antes de la línea que cierra el array con `];`.
3. Cambia `titulo`, `tipo`, `enunciado`, `pista`, `solucion`, etc.
4. Si es de modificación, añade `verificar` y `filasEsperadas`.
5. Guarda el archivo y pruébalo con doble clic.

### 3.3. Cambiar la base de datos y sus datos

- `DB_SCHEMA`: sentencias `CREATE TABLE` que crean las tablas.
- `DB_SEED`: sentencias `INSERT INTO` con los datos de ejemplo.

Si añades una tabla nueva, actualiza también el esquema visual que ve el
alumno dentro de la función `pintarEsquema()` (línea con los nombres de
tablas en el `pre`), o aparecerá igualmente en la lista de tablas
`['autores','libros','socios','prestamos']` de esa función.

### 3.4. Cuidado con los ejercicios de modificación

Para `update` y `delete`, `verificar` debe devolver el estado **completo**
de las filas implicadas (o de toda la tabla), para que un cambio de más filas
de las debidas no pase desapercibido. Combínalo siempre con `filasEsperadas`.

---

## 4. Cómo funciona el "Comprobar"

1. Se crea una **base de datos nueva** desde cero (siempre con los mismos datos).
2. Se ejecuta la consulta del alumno.
   - Si hay error SQL, se muestra traducido al español.
3. Según el tipo:
   - **select**: se compara el resultado del alumno con el resultado de la solución (`ordenSensible` decide si el orden importa).
   - **insert/update/delete**: se comprueba primero `filasEsperadas` y después se ejecuta el `verificar` del alumno y el de la solución para comparar el estado final de los datos.
4. Si coincide todo, el ejercicio queda marcado como resuelto (✓) y suma al contador de progreso. Si no, se indican las diferencias concretas.

---

## 5. Diferencias MySQL vs SQLite (importante en clase)

Este motor usa SQLite, **no MySQL**. Para el nivel básico de estos
ejercicios el comportamiento es el mismo, pero conviene que lo avises a tus
alumnos para que no se lleven sorpresas:

| Tema | MySQL | SQLite (este motor) |
|------|-------|----------------------|
| Cláusulas SELECT/WHERE/ORDER BY/LIMIT | igual | igual |
| JOIN ... ON | igual | igual |
| Contador automático | `AUTO_INCREMENT` | `INTEGER PRIMARY KEY AUTOINCREMENT` |
| Tipos | `INT`, `VARCHAR(n)`, `DATE`, `DECIMAL`… | `INTEGER`, `TEXT`, `REAL`, `BLOB` |
| Comillas para textos | `'...'` o `"..."` | `'...'` (las dobles son para identificadores) | 
| Comillas para nombres de tabla/columna | backticks `` ` `` | admite backticks y comillas dobles |
| `LIMIT` | ok | ok (al final de la consulta) |
| Funciones (COUNT, SUM, UPPER, LOWER, ROUND) | ok | ok (la mayoría idénticas) |
| Fechas | tipo DATE con funciones propias | se guardan como TEXT `'AAAA-MM-DD'` |

> Consejo: en los ejercicios evita intencionadamente temas que sí difieren
> (funciones de fecha, tipos decimales, `AUTO_INCREMENT`, backticks) para no
> confundir a quien luego trabaje con MySQL.

---

## 6. Evaluación (opcional)

No hay nota automática ni registro de respuestas: los resultados son
totalmente locales (privacidad del alumnado). Si quieres usar la actividad
como evaluación, puedes:

- Asignar una puntuación manual según los ejercicios resueltos (por ejemplo
  10 puntos × ejercicio = **210 puntos**).
- Pedir una captura de pantalla final con el contador de progreso completo.
- Pedir en el examen que **escriban en papel o en un documento** las
  soluciones de N ejercicios.

> ⚠️ Las soluciones están visibles en el código fuente de `index.html`
> (bloque `EJERCICIOS`). Para usarla como actividad práctica durante la
> clase no es un problema; para un examen "cerrado" habría que crear una
> versión sin soluciones incrustadas.

---

## 7. Fallos típicos que la app detecta automáticamente

- Tabla o columna inexistentes.
- Errores de sintaxis (comas, punto y coma, comillas sin cerrar).
- Mezclar columnas de dos tablas sin `JOIN` o sin prefijar la tabla.
- `UPDATE` / `DELETE` sin `WHERE` (modifica demasiadas filas).
- Violación de clave foránea o de unicidad.
- Número de filas o columnas distinto del esperado.