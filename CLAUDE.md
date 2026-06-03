# MCP E-commerce Server — CLAUDE.md

## Descripción del Proyecto
Servidor MCP para automatización de tiendas en Shopify y MercadoLibre. Permite gestionar productos, actualizar precios en bulk, generar descripciones SEO con IA (Claude/OpenAI) y responder preguntas de clientes automáticamente.

## Stack
| Capa | Tecnología |
|------|-----------|
| Protocolo | MCP (Model Context Protocol) — `mcp` Python SDK |
| Lenguaje | Python 3.10+ |
| Runtime | stdio_server |
| Shopify | Shopify Admin REST API |
| MercadoLibre | MercadoLibre API v2 |
| AI | Anthropic Claude API |
| Config | python-dotenv |

## Estructura
```
mcp-ecommerce/
├── server.py           # Entry point — registra tools MCP
├── tools/
│   ├── shopify.py      # CRUD de productos, variantes, órdenes en Shopify
│   ├── mercadolibre.py # CRUD de listings, precios, preguntas en ML
│   └── ai_content.py   # Generación de descripciones SEO con IA
├── utils/
│   └── (helpers)
├── requirements.txt
└── setup.bat
```

## Herramientas MCP disponibles
| Tool | Plataforma | Descripción |
|------|-----------|-------------|
| `list_products` | Shopify / ML | Lista productos de la tienda |
| `generate_descriptions` | Ambas | Genera descripciones SEO con IA para productos |
| `update_description` | Ambas | Actualiza descripción de un producto |
| `bulk_update_prices` | Shopify | Actualiza precios de múltiples variantes |
| `update_ml_price` | MercadoLibre | Actualiza precio de una publicación |
| `get_unanswered_questions` | MercadoLibre | Lista preguntas sin responder |
| `auto_answer_questions` | MercadoLibre | Responde preguntas automáticamente con IA |
| `get_orders` | Shopify | Obtiene pedidos recientes |

## Comandos
```bash
# Setup
setup.bat

# Instalar dependencias
pip install -r requirements.txt

# Correr servidor
python server.py
```

## Variables de Entorno (.env)
```
ANTHROPIC_API_KEY=sk-ant-...
SHOPIFY_STORE_URL=https://tu-tienda.myshopify.com
SHOPIFY_ACCESS_TOKEN=shpat_...
ML_CLIENT_ID=...
ML_CLIENT_SECRET=...
ML_ACCESS_TOKEN=...
ML_REFRESH_TOKEN=...
```

## Convenciones
- Cada tool retorna `list[types.TextContent]` con JSON string
- Errores → `{"error": "mensaje"}` — nunca levantar excepciones no capturadas
- Separar lógica de Shopify vs MercadoLibre en archivos distintos (`tools/shopify.py`, `tools/mercadolibre.py`)
- AI content → siempre en `tools/ai_content.py` — no mezclar con API calls a plataformas

## GitHub
Repo: `cdgutierrez6/mcp-ecommerce` (crear si no existe)
Branch default: `main`

## Estado actual
v1.0 — funcional para uso con Claude Desktop.

## Reglas de trabajo
1. Credenciales → siempre en `.env`, nunca hardcodeadas
2. MercadoLibre tokens → manejar refresh automático en `tools/mercadolibre.py`
3. AI descriptions → cachear si el producto no cambió para no gastar tokens
