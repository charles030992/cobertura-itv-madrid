# Cobertura ITV Madrid

Proyecto de análisis geoespacial sobre estaciones ITV y su relación con
zonas industriales en la Comunidad de Madrid, desarrollado con ArcGIS
Online, expresiones Arcade y smart mapping.

## Estado actual

- Cuenta ArcGIS Location Platform creada (portal: `charlesjedi.maps.arcgis.com`)
- Dataset de prueba con datos ficticios (matrículas y coordenadas inventadas,
  fechas de ITV variadas para testear lógica de alertas)
- Capas `vehiculos_itv`, `estaciones_itv` y `zonas_industriales` publicadas
  como hosted feature layers
- Expresión Arcade de clasificación por urgencia de ITV, aplicada como
  simbología de tipos únicos
- Análisis de cobertura (radio 15 km) mediante Popup Arcade, con hallazgo
  confirmado: 4 de 12 zonas industriales fuera de cobertura
- Smart mapping continuo sobre `distancia_km`: gradiente de color por
  distancia a la estación ITV más cercana

## Datos

| Archivo                       | Contenido                                                                    |
| ----------------------------- | ---------------------------------------------------------------------------- |
| `data/vehiculos_itv.csv`      | 12 vehículos ficticios: matrícula, tipo, fecha ITV, delegación, coordenadas  |
| `data/estaciones_itv.csv`     | 8 estaciones ITV (municipios de Madrid)                                      |
| `data/zonas_industriales.csv` | 12 polígonos industriales (municipios de Madrid), con campo `distancia_km`   |

## Hallazgo principal

Con un radio de cobertura de 15 km, 4 de las 12 zonas industriales
analizadas quedan sin cobertura: Aranjuez, Colmenar Viejo, Arganda del Rey
y San Martín de la Vega. El patrón es consistente — las zonas industriales
del sur y este de la Comunidad de Madrid quedan sistemáticamente fuera del
alcance de cualquier estación ITV.

## Mapa

[Semáforo ITV - Flota LICUAS (prueba)](https://arcg.is/1T59Oi4)

## Limitaciones conocidas

El tipo de usuario **Location Platform Owner** no incluye extensiones de
análisis (Creator, ArcGIS Image, raster analytics). Esto condiciona el
proyecto en dos puntos:

- El buffer de cobertura es aproximado (cálculo de distancia vía Arcade en
  Popup), no geometría real. El buffer real está pendiente de resolver con
  ArcGIS API for Python.
- No es posible ejecutar modelos GeoAI preentrenados sobre imagen.

## Próximos pasos

- Buffer real de 15 km vía ArcGIS API for Python
- Decidir el enfoque GEOAI viable dentro de las limitaciones de licencia
- Retoques finales de diseño y presentación del mapa
- Aplicación al concurso Esri "Mapas en Acción" (CEsri26), deadline ~20 de
  septiembre de 2026

## Notas técnicas

Ver `docs/notas-avance.md` para el detalle de decisiones tomadas y
problemas resueltos durante el desarrollo, y `docs/handoffs/` para el
estado de cada sesión de trabajo.

