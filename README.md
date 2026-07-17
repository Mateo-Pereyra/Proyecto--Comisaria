# Denuncias Policiales Perú 2018 - Abril 2026

Aplicación web interactiva para el análisis exploratorio y visual de las denuncias policiales registradas en el SIDPOL (Sistema de Denuncias Policiales), que permite identificar patrones delictivos por región, tipo de delito y periodo, e informar la toma de decisiones en política pública.

## Objetivo

Según el Observatorio Nacional de Seguridad Ciudadana, las denuncias por robos, hurtos y delitos contra la vida se han incrementado significativamente en los últimos años, concentrándose principalmente en Lima. Este proyecto responde a preguntas clave como: ¿qué comisarías concentran más carga de denuncias?, ¿qué tan lejos está, en promedio, una denuncia de su comisaría de referencia?, ¿cómo se distribuyen los tipos de delito por distrito o comisaría?

**Objetivos específicos:**
- Consumir de forma automatizada y robusta los datos de la API de Datos Abiertos del Ministerio del Interior.
- Exponer un dashboard interactivo en Streamlit con filtros dinámicos, KPIs y múltiples visualizaciones.

## Highlights técnicos

- 🗺️ **Asignación geoespacial de comisarías:** cálculo de distancia Haversine entre cada distrito y su comisaría más cercana, sobre +357K registros y 1,839 distritos.
- 🔧 **Corrección de una limitación metodológica propia:** se detectó que 155 distritos tienen más de una comisaría real (ej. San Juan de Lurigancho con 9, Callao con 11) — el método de vecino más cercano las ignoraba. Se diseñó una tabla de referencia con reparto proporcional por el campo oficial `SECTORES` de la PNP, en vez de asumir un criterio arbitrario.
- 🌦️ **Integración de API externa:** consumo de la API pública Open-Meteo para datos climáticos en tiempo real, con diferenciación geoespacial explícita entre Lima Metropolitana y Región Lima.
- 🗄️ **Persistencia en SQLite:** base de datos local con tablas `denuncias` e `historial_clima`, alimentada mediante inserts parametrizados (prevención de inyección SQL) cada vez que el usuario interactúa con los filtros.
- 🛡️ **Manejo de errores robusto:** decorador personalizado (`@Log_tiempo`) con `functools.wraps` para medir tiempos de ejecución y capturar fallos de la API sin colapsar la aplicación, con logging centralizado.
- 📊 **Dashboard multi-vista en Streamlit:** filtros dinámicos en cascada, KPIs en tiempo real, mapa interactivo (scatter mapbox), rankings de comisarías y exportación de datos a CSV.

## Fuentes de datos

- **Dataset principal:** 357,991 registros de denuncias policiales (SIDPOL, enero 2018 – abril 2026), publicados por el Ministerio del Interior en el Portal de Datos Abiertos del Estado Peruano.
- **Bases de comisarías PNP:** combinación de un Excel oficial con coordenadas GPS (2019, desactualizado) y un PDF con el listado vigente de comisarías (2025, sin coordenadas) para maximizar cobertura y precisión.
- **Coordenadas de distritos:** repositorio abierto [`ubigeos_peru`](https://github.com/MichaelSuarez0/ubigeos_peru), usado como referencia geográfica ante el bloqueo de scraping en los portales oficiales.
- **Clima:** API pública [Open-Meteo](https://open-meteo.com).

## Módulos de la aplicación

1. **Resumen:** ranking de departamentos, participación por modalidad de delito, evolución temporal, mapa de calor mes vs. año, alertas de variación porcentual interanual y comparador entre zonas.
2. **Comisarías:** top 10 de dependencias con mayor carga, ranking dinámico controlado por slider, distancia promedio distrito-comisaría por departamento.
3. **Mapa:** visualización geoespacial interactiva de comisarías con ajuste automático de centro y zoom según filtros.
4. **Datos:** tabla con buscador integrado y exportación a CSV.

## Limitaciones conocidas

- La distancia se calcula en línea recta (Haversine), no considera red vial real — puede no reflejar accesibilidad en zonas de selva o sierra.
- El dataset original está agregado a nivel de distrito; el reparto entre comisarías de un mismo distrito es una estimación analítica, no un dato observado directamente.
- 30 comisarías del listado 2025 no cuentan con coordenadas reportadas y fueron excluidas de los cálculos espaciales.

## Conclusiones

- Lima Metropolitana concentra más de 2.5 millones de denuncias, muy por encima del resto del país, liderada por San Juan de Lurigancho y Lima Cercado.
- La violencia contra la mujer e integrantes del hogar representa el 26.3% de las denuncias específicas, seguida de hurto (20.2%) y robo (10.5%).
- La tendencia muestra un crecimiento sostenido hasta un pico en 2023, con una caída hacia 2026 que debe interpretarse con cautela por tratarse de un año incompleto.

## Stack

Python (pandas) · Streamlit · Plotly · SQLite · Open-Meteo API


## Autores

Mateo Pereyra · Rosalinda Salva · Marcelo Torres · Alejandra Medina 
