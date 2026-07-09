# Handoff — 2026-07-09

## Estado
Cuenta ArcGIS Location Platform (`charlesjedi.maps.arcgis.com`) sin Análisis
ni Notebooks. Capas publicadas: vehiculos_itv, estaciones_itv,
zonas_industriales.

## Qué se intentó hoy
Calcular cobertura (distancia a estación ITV) vía Arcade
(FeatureSetByPortalItem) en una expresión de Simbología, para colorear
zonas_industriales sin usar la herramienta de Análisis. No funciona:
restricción de arquitectura (FeatureSet solo en perfiles de un evento,
no en Simbología). Alternativa que sí funciona: Popup (texto, no color).

## Resultado actual
Con radio 15 km vía Popup, las 8 zonas industriales salen "Cubiertas".
Sin hallazgo real todavía — dataset de prueba generado cerca de
estaciones.

## Siguiente paso técnico (para Claude Code)
1. Autenticación OAuth interactiva vía navegador, Python local
   (GIS(portal_url, client_id=...), sin usuario/contraseña pegados).
2. Buffer de 15 km sobre estaciones_itv, con dissolve.
3. Publicar como buffer_15km_itv.
4. Cruzar contra zonas_industriales para cobertura real.
5. Decidir después si hace falta bajar el radio o añadir zonas más
   dispersas para generar un hallazgo real (no forzar el resultado antes).

## Repo
Clonado en C:\Users\carlo\VStudio\cobertura-itv-madrid