# Handoff — 2026-07-09 (tarde)

## Estado
App OAuth registrada en el portal (tipo "Otra aplicación", redirect URI
urn:ietf:wg:oauth:2.0:oob). .env local con ARCGIS_CLIENT_ID y ARCGIS_URL,
protegido por .gitignore. Repo local: C:\Users\carlo\VStudio\cobertura-itv-madrid

## Siguiente paso técnico
Escribir script Python (arcgis.gis.GIS con client_id) que:
1. Haga login interactivo por navegador.
2. Cree buffer de 15 km sobre estaciones_itv con dissolve.
3. Publique como buffer_15km_itv.
4. Cruce contra zonas_industriales para cobertura real.

## Recordatorio
Hacer `git pull` al abrir la próxima sesión antes de nada.
