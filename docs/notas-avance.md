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

## 2026-07-09 — Buffer bloqueado por permisos, alternativa Popup

### Bloqueo: análisis de cobertura vía Arcade en Estilos

Intentado: calcular distancia mínima a estación ITV usando
FeatureSetByPortalItem dentro de una expresión de Simbología, para
colorear zonas_industriales por cobertura sin usar la herramienta de
Análisis (bloqueada por tipo de usuario, ver nota anterior).

Resultado: no funciona. Las funciones FeatureSet (incluida
FeatureSetByPortalItem) no están permitidas en el perfil de
Visualización/Estilos — solo en perfiles que se ejecutan una vez por
evento (Popup, Field Calculate), no en los que se repintan miles de
veces al hacer zoom/pan. Es una restricción de arquitectura de Arcade,
no un bug.

Alternativa viable en el mismo perfil: sí funciona en Popup — se puede
mostrar la distancia/cobertura como texto al hacer clic en cada zona,
pero no como color de simbología.

Pendiente: resolver cobertura real vía buffer con ArcGIS API for
Python (Claude Code), igual que el desbloqueo de OAuth en el otro
proyecto.

### Resultado (con distancia aproximada vía Popup)

Con radio de 15 km, las 8 zonas industriales salen clasificadas como
"Cubierta" — no hay ningún hallazgo de falta de cobertura con este
dataset y este radio. Es un resultado esperado: las zonas de prueba
se generaron cerca de municipios con estación ITV.

Decisión: no forzar el resultado ahora. Para generar un hallazgo real,
hay dos vías pendientes de evaluar más adelante — bajar el radio, o
añadir zonas industriales ficticias más dispersas. Se decide cuando
el buffer geométrico real (vía Claude Code) esté resuelto, con datos
más fiables que esta aproximación por distancia.

Idea de evolución futura del proyecto (no en el alcance del concurso):
añadir variables adicionales de flota (repostaje, mantenimiento) para
enriquecer el análisis más allá de solo ITV.
