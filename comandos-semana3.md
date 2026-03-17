# Comandos Semana 3 — Bot Telegram con IA
## TicoMart S.A.

---

## Herramientas utilizadas

| Herramienta | Uso | Costo |
|-------------|-----|-------|
| Telegram BotFather | Crear el bot | Gratis |
| Groq API | IA para analizar métricas | Gratis |
| N8N | Automatización del flujo | Gratis (self-hosted) |

---

## Paso 1 — Crear el bot de Telegram

1. Abrí Telegram y buscá @BotFather
2. Escribile `/newbot`
3. Seguí las instrucciones — te da un token
4. Buscá tu bot y escribile `/start` para activarlo
5. Obtené tu Chat ID abriendo esta URL en el navegador:
```
https://api.telegram.org/bot<TOKEN>/getUpdates
```

---

## Paso 2 — Obtener API Key de Groq

1. Entrá a https://console.groq.com
2. Creá una cuenta gratuita
3. Andá a API Keys → Create API Key
4. Guardá el key generado

---

## Paso 3 — Flujo N8N

### Nodo 1 — Schedule Trigger
- Trigger interval: Minutes
- Minutes between triggers: 5

### Nodo 2 — HTTP Request (Prometheus)
- Method: GET
- URL:
```
http://ticomart-prometheus:9090/api/v1/query?query=100-(avg(rate(node_cpu_seconds_total{mode="idle"}[5m]))*100)
```

### Nodo 3 — Code in JavaScript
```javascript
const data = $input.first().json;
const result = data.data.result[0];
const cpuUsage = parseFloat(result.value[1]).toFixed(2);

return [{
  json: {
    cpu: cpuUsage,
    message: `TicoMart - Reporte del Servidor\n\nCPU: ${cpuUsage}%\nHora: ${new Date().toLocaleString('es-CR', {timeZone: 'America/Costa_Rica'})}`
  }
}];
```

### Nodo 4 — HTTP Request (Groq IA)
- Method: POST
- URL: `https://api.groq.com/openai/v1/chat/completions`
- Headers:
  - Authorization: Bearer TU_GROQ_API_KEY
  - Content-Type: application/json
- Body (JSON, modo Fixed):
```json
{
  "model": "llama-3.1-8b-instant",
  "messages": [
    {
      "role": "system",
      "content": "Eres un analista de infraestructura IT. Analiza las metricas del servidor y da una respuesta corta en español con el estado del servidor y alguna recomendacion si es necesario."
    },
    {
      "role": "user",
      "content": "={{ $('Code in JavaScript').item.json.message }}"
    }
  ]
}
```

### Nodo 5 — Telegram
- Operation: Send a text message
- Credential: token del bot de Telegram
- Chat ID: TU_CHAT_ID
- Text (modo Expression):
```
{{ $('Code in JavaScript').item.json.message }}

Análisis IA:
{{ $('HTTP Request1').item.json.choices[0].message.content }}
```

---

## Estado final Semana 3

| Componente | Estado |
|-----------|--------|
| Bot Telegram @ticomartbot | Activo |
| Groq API | Configurada |
| Flujo N8N | Publicado |
| Reportes automáticos cada 5 min | Funcionando |
