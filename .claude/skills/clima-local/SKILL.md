---
name: clima-local
description: Use when the user asks about the current weather, temperature, or forecast for their location or a specific city (e.g. "cómo está el clima", "qué tiempo hace en Lima", "va a llover hoy"). Fetches live weather data from the command line with no API key required.
---

# Clima local

Consulta el clima actual o el pronóstico usando el servicio público `wttr.in` directamente desde la terminal, sin necesidad de API key ni registro.

## Cómo usarlo

1. Determina la ubicación:
   - Si el usuario especifica una ciudad, úsala (ej. "Lima", "Mexico+City" con `+` en vez de espacios).
   - Si no especifica nada, omite la ciudad para que `wttr.in` detecte la ubicación por IP.

2. Ejecuta el comando con Bash:

   ```bash
   curl -s "wttr.in/<ciudad>?lang=es&m&Q" 
   ```

   Parámetros útiles:
   - `lang=es` — respuesta en español.
   - `m` — unidades métricas (°C, km/h).
   - `Q` — quita el encabezado/URL del reporte, deja solo el reporte.
   - Formato compacto de una línea: agrega `&format=%l:+%C+%t+%w+%h` para ciudad, condición, temperatura, viento y humedad.
   - Para solo texto sin gráficos ASCII: usa el formato compacto anterior en vez del reporte completo de 3 días.

3. Si el comando falla (sin internet, servicio caído, ciudad no encontrada), informa al usuario claramente en vez de inventar datos.

4. Resume el resultado en español de forma breve y natural (temperatura, sensación, condición, viento), no pegues el ASCII art completo salvo que el usuario lo pida explícitamente.

## Ejemplos

- "¿Cómo está el clima?" → `curl -s "wttr.in/?lang=es&m&Q&format=%l:+%C+%t+%w+%h"`
- "¿Qué clima hace en Bogotá?" → `curl -s "wttr.in/Bogota?lang=es&m&Q&format=%l:+%C+%t+%w+%h"`
- "Dame el pronóstico de 3 días de Lima" → `curl -s "wttr.in/Lima?lang=es&m&Q"` (sin `format`, para ver el reporte completo)
