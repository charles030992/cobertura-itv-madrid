# Handoff — 2026-07-23

## Estado

12 zonas industriales publicadas con campo `distancia_km`. Smart
mapping continuo aplicado y funcionando (gradiente por distancia a
estación ITV). Vehículos como capa protagonista, zonas como contexto
con contorno negro. Diseño fino aplazado a fase final.

## Qué se hizo hoy

1. **Smart mapping resuelto.** Rampa de color invertida para que
   lejos = rojo. Aprendizaje: el estilo continuo sustituye el símbolo
   (no convive con el icono de fábrica), y un campo ya asignado
   desaparece de la lista de "+ Campo" — se edita por Opciones de
   estilo.

2. **Jerarquía visual.** Decidido que los vehículos son los
   protagonistas. Contorno negro en zonas para separarlas de los
   pines.

3. **GEOAI: verificación de viabilidad → bloqueado.** Ver abajo.

## Bloqueo de licencia (cerrado, no reabrir)

Panel de Licencias confirma que **Location Platform Owner** incluye
solo Map Viewer, Organization Website, Scene Viewer y Native Maps
SDK Lite. Sin ArcGIS Image, sin Image Analyst, sin raster analytics.

Queda descartado:
- Ejecutar modelos GeoAI preentrenados (.dlpk) sobre imagen.
- Foundation models / location embeddings + AutoML (requiere Pro
  Advanced).
- Reutilizar el YOLO de `arcgis-yolo-infraestructuras`: es COCO
  (80 clases de objetos cotidianos), no geoespacial. Encaje forzado.

Esto cierra las vías que el handoff del 14-07 dejaba abiertas.

## Restricción adicional detectada

Aunque hubiera licencia, la imagen de World Imagery de Esri tiene
limitaciones de uso: los derivados son solo para uso no comercial, no
pueden compartirse fuera de la organización, y World Imagery no puede
usarse como entrada directa para extracción automatizada de
información. A tener en cuenta si en algún momento se reconsidera
esta vía.

## Decisión pendiente (siguiente sesión)

Única vía GEOAI compatible con la licencia: consumir capas
GeoAI-derived ya publicadas en Living Atlas (tipo land cover global)
como feature layer normal.

Contra: resolución media (~10 m) y solo distingue "área construida"
genérica, sin separar industrial de residencial. No valida las 12
zonas concretas del proyecto.

A decidir: incorporarlo igualmente, reorientar el análisis hacia
"áreas construidas sin cobertura", o concluir que GEOAI no encaja de
forma honesta aquí y reforzar los otros dos criterios (uso
inteligente del dato, Living Atlas por otras capas).

## Siguiente paso técnico (para Claude Code)

Retomar el buffer real de 15 km vía ArcGIS API for Python. Pendiente
desde el 09-07; la primera prueba falló y el error nunca se
diagnosticó. Es la única tarea que requiere Claude Code y sigue sin
resolverse.

## Contexto temporal

Deadline del concurso CEsri26 ("Mapas en Acción"): ~20 de septiembre
de 2026.
