# Handoff — 2026-07-14

## Estado

12 zonas industriales (8 originales + 4 nuevas: Aranjuez, Colmenar
Viejo, Arganda del Rey, San Martín de la Vega). Cobertura vía Popup
Arcade (distancia mínima a estación ITV, radio 15 km).

## Qué se hizo hoy

Ampliación del dataset para generar un hallazgo real de cobertura.
Lección técnica: editar el CSV en GitHub no actualiza la capa en
ArcGIS Online — hay que usar "Actualizar datos → Sobrescribir capa
de entidades entera" sobre el ítem, subiendo el CSV descargado tras
el commit. "Exportar datos" no sirve para esto (genera copia de los
datos existentes, no permite subir nuevos).

## Resultado

Con radio de 15 km: 4 zonas "No cubierta" (las 4 nuevas, confirmado
una por una), 8 "Cubierta" (dataset original). Hallazgo consistente,
no un caso aislado — zonas al sur/este de la Comunidad de Madrid
quedan sistemáticamente fuera de cobertura ITV.

## Nuevo requisito (email organización CEsri26)

Valoran explícitamente: uso inteligente del dato, GEOAI, integración
con Living Atlas. GEOAI no está cubierto en este proyecto todavía.

## Siguiente paso técnico (para Claude Code)

1. Retomar buffer real vía ArcGIS API for Python (pendiente desde el
   09-07, sin diagnosticar el error de la primera prueba).
2. Explorar opciones concretas de GEOAI viables antes del 20 de
   septiembre — posible conexión con el proyecto
   arcgis-yolo-infraestructuras (YOLO ya funcional ahí), o alternativa
   más ligera si conectar ambos proyectos complica demasiado el
   alcance.

## Repo

Clonado en local (ver configuración de cada máquina).
