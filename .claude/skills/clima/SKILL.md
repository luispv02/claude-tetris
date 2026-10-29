---
name: clima
description: Consulta el clima actual y el pronóstico de la ubicación actual del usuario (detectada por IP). Úsala cuando pida el clima, la temperatura, si va a llover o el pronóstico de los próximos días.
allowed-tools: Bash(curl *)
---

# Clima de la ubicación actual

Obtiene el clima de la ubicación actual del usuario usando [wttr.in](https://wttr.in), sin API key. Responde siempre en español.

## Pasos

1. Clima actual, en una línea (sin ciudad: wttr.in detecta la ubicación por IP):

   ```bash
   curl -s "wttr.in/?format=%l:+%c+%t+(sensación+%f),+humedad+%h,+viento+%w,+lluvia+%p&lang=es&m"
   ```

2. Si el usuario pidió pronóstico, o la pregunta lo requiere (¿lloverá?, ¿mañana?), usa el JSON:

   ```bash
   curl -s "wttr.in/?format=j1&lang=es"
   ```

   Campos útiles: `current_condition[0]` (`temp_C`, `FeelsLikeC`, `humidity`, `windspeedKmph`, `lang_es[0].value`),
   `weather[i]` (`date`, `maxtempC`, `mintempC`, `hourly[].chanceofrain`), `nearest_area[0]` (ubicación detectada).

## Formato de respuesta

- Indica la ubicación detectada, porque la geolocalización por IP puede ser imprecisa.
- Resumen corto: condición, temperatura (°C), sensación térmica, humedad, viento, probabilidad de lluvia.
- Para pronóstico, una línea por día con mín/máx y condición.
- Si el usuario pregunta algo práctico (¿llevo paraguas?), responde directo con base en los datos.

## Errores

- Si `curl` falla o devuelve vacío/HTML, informa que no se pudo consultar el servicio y sugiere reintentar.
