# What Matters Today

*[Read in English](README.md)*

Digest diario automatizado de noticias sobre **Apple, IA Software, IA (general) y Tech**, publicado cada mañana a las 8:00 (hora de España) en:

**👉 [whatmatterstoday.juanpecatalan.com](https://whatmatterstoday.juanpecatalan.com)**

Disponible en español e inglés, con selector de idioma en el propio sitio.

## Qué es

Cada día, un workflow recopila las noticias más relevantes de esos cuatro temas, las deduplica, genera un resumen (en español e inglés) y las publica como un digest fechado. El sitio muestra el digest del día (`index.html`) y un histórico navegable por fecha (`archive.html`).

## Cómo funciona

Todo el pipeline (fetch de fuentes → deduplicado → resumen/redacción → publicación) está orquestado con **[n8n](https://n8n.io)**, alojado fuera de este repositorio. Este repo **no contiene el workflow de n8n**: es únicamente el destino donde n8n publica los resultados y el sitio estático que los muestra.

Cada ejecución del workflow:

1. Genera el digest del día como JSON, con cada campo editorial redactado en español e inglés.
2. Lo añade a `data/archive/YYYY-MM-DD.json` y actualiza `data/latest.json`.
3. Actualiza `data/archive-index.json` con la lista de fechas disponibles.
4. Hace commit y push de los cambios a este repositorio.

El sitio estático (sin build ni backend) lee esos JSON directamente para renderizar el digest actual y el archivo histórico, en el idioma que elija cada lector.

## Estructura del repositorio

```
.
├── index.html              # Vista del digest del día (lee data/latest.json)
├── archive.html            # Vista del histórico (lee data/archive-index.json y data/archive/*.json)
├── data/
│   ├── latest.json         # Digest más reciente
│   ├── archive-index.json  # Índice de fechas disponibles en el archivo
│   └── archive/
│       └── YYYY-MM-DD.json # Digest de cada día
├── favicon-32.png, icon-512.png, apple-touch-icon.png
└── README.md
```

## Formato de un digest (`data/latest.json` / `data/archive/*.json`)

Cada campo de texto editorial es un objeto `{ "es": "...", "en": "..." }`. Los campos de metadata (`id`, `topic`, `source`, `link`, `image`) son strings planos.

```jsonc
{
  "date": "YYYY-MM-DD",
  "daily_summary": { "es": "Resumen general del día", "en": "..." },
  "topic_summaries": {
    "apple": { "es": "...", "en": "..." },
    "ai_software": { "es": "...", "en": "..." },
    "ai_general": { "es": "...", "en": "..." },
    "tech": { "es": "...", "en": "..." }
  },
  "top_stories": [
    {
      "id": "...",
      "title": { "es": "...", "en": "..." },
      "body": { "es": "...", "en": "..." },
      "topic": "apple | ai_software | ai_general | tech",
      "source": "Nombre del medio",
      "link": "https://...",
      "image": "https://..."
    }
  ],
  "quick_reads": [
    { "id": "...", "title": { "es": "...", "en": "..." }, "text": { "es": "...", "en": "..." } }
  ],
  "trending_repos": [
    { "id": "...", "why_it_matters": { "es": "...", "en": "..." } }
  ],
  "developer_signals": [
    { "id": "...", "text": { "es": "...", "en": "..." } }
  ]
}
```

El sitio también acepta el formato antiguo (un string plano en vez de `{es, en}`) para compatibilidad con digests generados antes de que el pipeline fuera bilingüe.

## Temas cubiertos

- **Apple** — productos, litigios, filtraciones, eventos.
- **IA Software** — herramientas, modelos y productos de desarrollo asistido por IA.
- **IA (general)** — adopción, investigación y aplicaciones de IA fuera del software puro.
- **Tech** — actualidad tecnológica general.

## Publicación

El sitio es estático (HTML + JSON, sin frameworks ni build step) y se sirve directamente desde este repositorio.
