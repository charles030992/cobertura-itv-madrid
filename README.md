# Cobertura ITV Madrid

Proyecto de análisis geoespacial sobre estaciones ITV y su relación con
zonas industriales en la Comunidad de Madrid, desarrollado con ArcGIS
Online y expresiones Arcade.

## Estado actual

- Cuenta ArcGIS Location Platform creada (portal: `charlesjedi.maps.arcgis.com`)
- Dataset de prueba con datos ficticios (matrículas y coordenadas inventadas,
  fechas de ITV variadas para testear lógica de alertas)
- Capa `vehiculos_itv` publicada como hosted feature layer
- Expresión Arcade de clasificación por urgencia de ITV, aplicada como
  simbología de tipos únicos

## Datos

| Archivo | Contenido |
|---|---|
| `data/vehiculos_itv.csv` | 12 vehículos ficticios: matrícula, tipo, fecha ITV, delegación, coordenadas |
| `data/estaciones_itv.csv` | 8 estaciones ITV (municipios de Madrid) |
| `data/zonas_industriales.csv` | 8 polígonos industriales (municipios de Madrid) |

## Próximos pasos

- Publicar `estaciones_itv` y `zonas_industriales`
- Análisis de cobertura (buffer/distancia) entre estaciones y zonas industriales
- Aplicación al concurso Esri "Mapas en Acción" (CEsri26)

## Notas técnicas

Ver `docs/notas-avance.md` para el detalle de decisiones tomadas y
problemas resueltos durante el desarrollo.
