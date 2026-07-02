# Notas de avance

## 2026-07-02 — Configuración inicial y primer semáforo Arcade

### Cuenta ArcGIS

- Cuenta pública existente no permite publicar hosted feature layers
  (solo cuentas organizacionales/Developer pueden hacerlo).
- La antigua "ArcGIS Developer account" se renombró a "ArcGIS Location
  Platform" (cambio de Esri de junio 2024). Gestión ahora en
  location.arcgis.com, no en developers.arcgis.com.
- Cuenta creada con email personal, portal `charlesjedi.maps.arcgis.com`
  (nota: la URL del portal no se puede cambiar tras la creación).

### Dataset de prueba

- Generado con datos ficticios pero coherentes geográficamente (municipios
  reales de Madrid, coordenadas reales, matrículas/nombres inventados).
- Fechas ITV escalonadas a propósito para poder probar clasificación por
  urgencia (vencidas, <30 días, 30-60 días, >60 días).

### Publicación de `vehiculos_itv.csv`

- Al subir el CSV, ArcGIS genera dos ítems: el CSV original y el
  Feature Layer (alojado). El que importa para el mapa es el segundo.
- En el asistente de publicación, el campo `fecha_itv` se detectó
  correctamente como tipo "Solo fecha" (DateOnly), sin intervención manual.

### Problema: expresión Arcade devolvía NaN

Causa: Arcade distingue entre el tipo `Date` (con hora/timezone, lo que
devuelve `Today()`) y `DateOnly` (solo fecha, lo que tienen los campos
importados desde CSV como "Solo fecha"). `DateDiff()` no puede operar
directamente entre un `Date` y un `DateOnly`.

Solución: convertir `Today()` a `DateOnly` explícitamente:

var dias = DateDiff($feature.fecha_itv, DateOnly(Today()), 'days')

### Problema: la simbología no reflejaba la expresión corregida

Causa: cada vez que se pulsaba "+ Expresión" en el editor de estilo se
creaba una expresión nueva en vez de editar la existente. La simbología
seguía apuntando a una expresión antigua (con el `Today()` sin corregir),
mientras las pruebas de depuración se hacían sobre expresiones distintas
no conectadas al estilo.

Lección: al editar una expresión Arcade ya usada en el estilo, entrar
por el lápiz de edición sobre la expresión existente, no crear una nueva.

### Resultado final

Clasificación por 4 categorías, aplicada como "Tipos (símbolos únicos)":

| Categoría | Rango | Recuento | Color |
|---|---|---|---|
| Vencida_Urgente | < 0 días | 2 | Rojo |
| < 30 dias | 0-29 días | 5 | Naranja/magenta |
| 30-60 dias | 30-59 días | 2 | Morado |
| > 60 dias | ≥ 60 días | 3 | Verde |

Mapa guardado como "Semáforo ITV - Flota LICUAS (prueba)".
