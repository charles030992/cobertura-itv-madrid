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

## 2026-07-09 (tarde) — Preparación OAuth para el buffer

- Registrada aplicación OAuth tipo "Otra aplicación" en el portal
  (charlesjedi.maps.arcgis.com), con Redirect URI
  urn:ietf:wg:oauth:2.0:oob.
- Creado `.env` local (no versionado) con ARCGIS_CLIENT_ID y ARCGIS_URL,
  y `.gitignore` para protegerlo.
- Pendiente: escribir el script Python que hace el login OAuth
  interactivo por navegador y calcula el buffer de 15 km sobre
  estaciones_itv.

  ## 2026-07-14 — Ampliación del dataset y hallazgo de cobertura real

### Problema detectado

Con las 8 zonas industriales originales, todas salían "Cubierta" a
15 km — resultado esperado pero poco interesante, porque el dataset
se diseñó con zonas muy cercanas a las estaciones.

### Solución

Se añadieron 4 zonas industriales nuevas, deliberadamente alejadas de
cualquier estación ITV: Aranjuez, Colmenar Viejo, Arganda del Rey y
San Martín de la Vega.

### Proceso técnico (lección aprendida)

Editar el CSV en GitHub no actualiza automáticamente la capa en
ArcGIS Online — son sistemas independientes sin sincronización. Hay
que descargar el CSV actualizado y usar explícitamente "Actualizar
datos → Sobrescribir capa de entidades entera" sobre el ítem Feature
Layer en ArcGIS. "Exportar datos" NO sirve para esto — genera una
copia de los datos existentes, no permite subir datos nuevos.

### Resultado

Con 12 zonas totales y radio de 15 km:
- Cubierta: 8 (dataset original)
- No cubierta: Aranjuez, Arganda del Rey, San Martín de la Vega
  (Colmenar Viejo pendiente de confirmar, previsiblemente también
  No cubierta)

Primer hallazgo real del proyecto: zonas industriales al sur/este de
la región (Aranjuez, Arganda, San Martín) quedan fuera del radio de
cobertura ITV de 15 km.

### Nuevo requisito detectado (email organización concurso)

CEsri26 valora explícitamente: uso inteligente del dato, integración
de GEOAI, y análisis apoyado en ArcGIS Living Atlas. GEOAI no está
cubierto todavía en este proyecto — posible conexión futura con el
proyecto `arcgis-yolo-infraestructuras` (ya tiene YOLO funcionando).

2026-07-14 — Ampliación del dataset y hallazgo de cobertura
Ampliación de zonas_industriales

Dataset ampliado de 8 a 12 zonas industriales. Las 4 nuevas (Aranjuez, Colmenar Viejo, Arganda del Rey, San Martín de la Vega) se eligieron deliberadamente en la periferia sur/este de la Comunidad de Madrid, para comprobar si el análisis de cobertura producía un hallazgo real o si todo el territorio quedaba cubierto.

Problema: editar el CSV en GitHub no actualiza la capa

Causa: el CSV del repositorio y el hosted feature layer de ArcGIS Online son artefactos independientes. Un commit en GitHub no propaga nada a la capa publicada.

Solución: descargar el CSV tras el commit y, sobre el ítem en ArcGIS Online, usar "Actualizar datos → Sobrescribir capa de entidades entera".

Nota: "Exportar datos" no sirve para esto — genera una copia de los datos ya existentes en la capa, no permite subir datos nuevos.

Resultado del análisis de cobertura (radio 15 km)
Estado	Nº zonas	Detalle
Cubierta	8	Dataset original
No cubierta	4	Las 4 zonas nuevas, verificadas una a una

Hallazgo consistente, no un caso aislado: las zonas industriales del sur y este de la Comunidad de Madrid quedan sistemáticamente fuera del radio de 15 km de cualquier estación ITV.

Nuevo requisito del concurso (email organización CEsri26)

La organización comunicó que valoran explícitamente tres criterios: uso inteligente del dato, GEOAI e integración con Living Atlas. GEOAI no estaba cubierto en el proyecto en esta fecha.

2026-07-23 — Smart mapping resuelto y GEOAI bloqueado por licencia
Smart mapping continuo sobre distancia_km

Objetivo: colorear las zonas industriales por distancia a la estación ITV más cercana (cerca = verde, lejos = rojo), cumpliendo el criterio de smart mapping que valora la organización.

Problema inicial: el resultado salía invertido (zonas lejanas en verde) y la capa mostraba el icono estático de fábrica en lugar del gradiente.

Causa y solución: el estilo continuo sustituye el símbolo por círculos coloreados — no es posible mantener el icono de fábrica y el gradiente a la vez. La inversión se corrigió invirtiendo la rampa de color desde Opciones de estilo.

Confusión a evitar: una vez añadido distancia_km con "+ Campo", ese campo desaparece de la lista de atributos disponibles, porque la lista solo muestra los que aún no están asignados. No es un error ni una capa equivocada; es el comportamiento normal de Map Viewer. Para modificar el estilo ya aplicado hay que entrar por Opciones de estilo, no por "+ Campo".

Legibilidad: colisión entre vehículos y zonas

Al pasar las zonas a símbolos redondos, chocaban visualmente con los pines de vehículos. Decisión: los vehículos son los protagonistas (el mapa trata de la flota); las zonas quedan como capa de contexto. Aplicado contorno negro a las zonas para delimitarlas. Los retoques finos (tamaño, transparencia, basemap con menos etiquetas, encuadre) se dejan para la fase final de diseño.

Observación pendiente para esa fase: aparecen etiquetas de municipios ajenos al análisis (Toledo, Tarancón). No son datos del proyecto, son etiquetas del mapa base, pero pueden inducir a error a quien vea el mapa. Se resuelve con encuadre y/o un basemap más neutro.

Bloqueo confirmado: GEOAI no ejecutable con esta licencia

Verificado en el panel de Licencias de la organización. El tipo de usuario Location Platform Owner incluye únicamente:

Map Viewer
Organization Website
Scene Viewer
Native Maps SDK Lite

No incluye ArcGIS Image, Image Analyst ni ninguna extensión de análisis ráster. Consecuencias:

Ejecutar modelos GeoAI preentrenados (.dlpk) sobre imagen queda descartado — requiere ArcGIS Image y una organización configurada para raster analytics.
La vía de foundation models / location embeddings + AutoML queda también descartada — requiere ArcGIS Pro con licencia Advanced.

No es un problema de configuración ni de permisos ocultos: esas capacidades sencillamente no están incluidas en el tipo de usuario.

Descarte del YOLO como vía GEOAI

El modelo YOLO del proyecto arcgis-yolo-infraestructuras está preentrenado sobre COCO (80 clases de objetos cotidianos). Es un detector de objetos de fotografía convencional, no un modelo geoespacial. Forzar su encaje con un análisis de cobertura ITV sería artificial. Se aparca como vía para este proyecto.

Vía que queda abierta (sin decidir)

Consumir capas ya generadas mediante GeoAI y publicadas en Living Atlas (p. ej. clasificación global de cobertura de suelo), que se añaden como feature layer normal y no requieren extensión.

Reserva honesta sobre esta opción: las capas globales de land cover tienen resolución media (~10 m) y solo distinguen "área construida" genérica — no diferencian suelo industrial de residencial. Como respaldo de las 12 zonas industriales concretas del proyecto es débil. Encajaría mejor en un análisis distinto ("qué áreas construidas de Madrid quedan fuera de cobertura").

Decisión pendiente: si merece la pena incorporar GEOAI por esta vía o si no encaja de forma honesta en este proyecto.
