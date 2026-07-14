# Cobertura ITV Madrid

Proyecto de análisis geoespacial sobre estaciones ITV y su relación con
zonas industriales en la Comunidad de Madrid, desarrollado con ArcGIS
Online y expresiones Arcade. Candidatura en preparación para el
concurso Esri "Mapas en Acción" (CEsri26, deadline 20 septiembre 2026).

## Estado actual

- Cuenta ArcGIS Location Platform activa (portal: `charlesjedi.maps.arcgis.com`)
- Dataset de prueba con datos ficticios (matrículas, coordenadas y
  fechas de ITV inventadas, pero geográficamente coherentes con
  municipios reales de la Comunidad de Madrid)
- Tres capas publicadas como hosted feature layers:
  - `vehiculos_itv` — con expresión Arcade de clasificación por
    urgencia de ITV (semáforo de 4 categorías)
  - `estaciones_itv` — 8 estaciones ITV
  - `zonas_industriales` — 8 polígonos industriales
- Análisis de cobertura (radio 15 km) implementado vía expresión
  Arcade en pop-up, calculando distancia mínima de cada zona
  industrial a la estación ITV más cercana

## Datos

| Archivo | Contenido |
|---|---|
| `data/vehiculos_itv.csv` | 12 vehículos ficticios: matrícula, tipo, fecha ITV, delegación, coordenadas |
| `data/estaciones_itv.csv` | 8 estaciones ITV (municipios de Madrid) |
| `data/zonas_industriales.csv` | 8 polígonos industriales (municipios de Madrid) |

## Mapa

[Semáforo ITV - Flota LICUAS (prueba)](https://arcg.is/1T59Oi4)

## Hallazgo actual

Con un radio de cobertura de 15 km y un dataset ampliado a 12 zonas
industriales, se detectan al menos 3 zonas sin cobertura ITV
suficiente: Aranjuez, Arganda del Rey y San Martín de la Vega —
todas al sur/este de la Comunidad de Madrid, alejadas del clúster
de estaciones concentrado en la zona sur-metropolitana.

## Limitaciones técnicas conocidas

- El análisis de buffer geométrico real (herramienta "Análisis" de Map
  Viewer) no está disponible: requiere tipo de usuario Creator/
  Professional, no incluido en la cuenta gratuita Location Platform.
  Alternativa aplicada: cálculo de distancia mínima vía Arcade en el
  perfil de Popup (las funciones FeatureSet no están permitidas en el
  perfil de Simbología).
- Dos de las tres capas (`estaciones_itv`, `zonas_industriales`) solo
  están compartidas a nivel de Organización, no público — pendiente de
  resolver antes de compartir el proyecto externamente.

Detalle completo de decisiones y problemas resueltos en
`docs/notas-avance.md`.

## Próximos pasos

0. Explorar integración GEOAI (posible conexión con proyecto
   arcgis-yolo-infraestructuras) — requisito explícito del concurso
1. Resolver buffer geométrico real vía ArcGIS API for Python (en curso)
2. Decidir radio de cobertura definitivo con datos de buffer real
3. Resolver compartición pública de las capas restantes
4. Smart mapping, integración con Living Atlas, diseño final
5. Redacción de la entrega para CEsri26

## Notas técnicas

Ver `docs/notas-avance.md` para el detalle de decisiones tomadas y
problemas resueltos durante el desarrollo.
