---
name: elevenlabs-agent-management
description: ElevenLabs Conversational AI agent management. Use when creating, updating, or configuring ElevenLabs voice agents, adding webhooks or server tools to agents, changing LLM settings, managing phone numbers, making outbound calls, or integrating ElevenLabs with n8n workflows.
---

# ElevenLabs Agent Management

Expert guide for managing ElevenLabs Conversational AI agents via MCP tools and REST API.

---

## Standard-Konfiguration

**LLM:** `gpt-4.1` (Standard für alle Agenten)
**TTS:** `eleven_v3_conversational` (Neustes Modell, 75+ Sprachen)
**Sprache:** `de` (für deutsche Agenten)

---

## MCP Tools (Verfügbar)

| Tool | Beschreibung |
|------|-------------|
| `mcp__elevenlabs__create_agent` | Agent erstellen |
| `mcp__elevenlabs__get_agent` | Agent-Details abrufen |
| `mcp__elevenlabs__list_agents` | Alle Agenten auflisten |
| `mcp__elevenlabs__add_knowledge_base_to_agent` | Knowledge Base hinzufügen |
| `mcp__elevenlabs__make_outbound_call` | Ausgehenden Anruf starten |
| `mcp__elevenlabs__list_phone_numbers` | Telefonnummern auflisten |
| `mcp__elevenlabs__list_conversations` | Gespräche auflisten |
| `mcp__elevenlabs__get_conversation` | Gespräch mit Transkript abrufen |
| `mcp__elevenlabs__search_voices` | Eigene Voices suchen |
| `mcp__elevenlabs__search_voice_library` | Öffentliche Voices suchen |
| `mcp__elevenlabs__text_to_speech` | Text zu Sprache |
| `mcp__elevenlabs__check_subscription` | Abo-Status prüfen |

---

## Lücken (NUR via REST API)

Es gibt **KEIN MCP-Tool** für:
- Agent aktualisieren → `PATCH /v1/convai/agents/{agent_id}`
- Agent löschen → `DELETE /v1/convai/agents/{agent_id}`
- Webhooks konfigurieren → Über Agent-Update
- Tools hinzufügen/ändern → Über Agent-Update
- LLM nach Erstellung ändern → Über Agent-Update

**API Base URL:** `https://api.elevenlabs.io`
**Auth Header:** `xi-api-key: YOUR_API_KEY`

---

## Agent erstellen (MCP)

```
mcp__elevenlabs__create_agent({
  name: "Agent Name",
  first_message: "Begrüßung...",
  system_prompt: "System Prompt...",
  llm: "gpt-4.1",
  language: "de",
  temperature: 0.5,
  voice_id: "VOICE_ID",
  model_id: "eleven_v3_conversational",
  stability: 0.4,
  similarity_boost: 0.8,
  optimize_streaming_latency: 3,
  turn_timeout: 7,
  max_duration_seconds: 600,
  record_voice: true
})
```

---

## Agent aktualisieren (REST API)

```
PATCH https://api.elevenlabs.io/v1/convai/agents/{agent_id}
Header: xi-api-key: YOUR_API_KEY
```

### LLM ändern
```json
{"conversation_config":{"agent":{"prompt":{"llm":"gpt-4.1","temperature":0.5}}}}
```

### System-Prompt ändern
```json
{"conversation_config":{"agent":{"prompt":{"prompt":"Neuer Prompt..."}}}}
```

### Voice ändern
```json
{"conversation_config":{"tts":{"voice_id":"NEUE_ID","model_id":"eleven_v3_conversational"}}}
```

### First Message ändern
```json
{"conversation_config":{"agent":{"first_message":"Neue Begrüßung..."}}}
```

---

## Webhooks konfigurieren

### Conversation Initiation Webhook
```json
PATCH /v1/convai/agents/{agent_id}
{
  "platform_settings": {
    "workspace_overrides": {
      "conversation_initiation_client_data_webhook": {
        "url": "https://n8n.example.com/webhook/init",
        "request_headers": {"Authorization": "Bearer TOKEN"}
      }
    },
    "overrides": {
      "conversation_config_override": {
        "agent": {
          "prompt": {"prompt": true},
          "first_message": true
        }
      }
    }
  }
}
```

### Post-Call Webhook
```json
PATCH /v1/convai/agents/{agent_id}
{
  "platform_settings": {
    "workspace_overrides": {
      "webhooks": {
        "events": ["transcript", "audio"]
      }
    }
  }
}
```

---

## Server Tools (Webhooks) hinzufügen

> **WICHTIG**: `tools`-Array wird komplett ersetzt! Immer ALLE Tools mitschicken.

```json
PATCH /v1/convai/agents/{agent_id}
{
  "conversation_config": {
    "agent": {
      "prompt": {
        "tools": [
          {
            "type": "webhook",
            "name": "tool_name",
            "description": "Wann und warum nutzen...",
            "response_timeout_secs": 15,
            "api_schema": {
              "url": "https://n8n.example.com/webhook/endpoint",
              "method": "POST",
              "content_type": "application/json",
              "request_headers": {
                "X-API-Key": "{{secret__api_key}}"
              },
              "request_body_schema": {
                "type": "object",
                "properties": {
                  "param": {"type": "string", "required": true, "description": "..."}
                }
              }
            }
          },
          {
            "type": "system",
            "name": "end_call",
            "description": "Beendet das Gespräch."
          }
        ]
      }
    }
  }
}
```

### Tool-Typen
| Typ | Verwendung |
|-----|-----------|
| `webhook` | HTTP-Aufruf an n8n/externe APIs |
| `system` | `end_call`, `transfer_to_number`, `transfer_to_agent`, `language_detection` |
| `client` | Browser/App-seitige Aktionen |
| `mcp` | MCP Server Integration |

### Tool-Optionen
| Option | Werte | Standard |
|--------|-------|---------|
| `response_timeout_secs` | 5-60 | 20 |
| `execution_mode` | `immediate`, `post_tool_speech`, `async` | `immediate` |
| `tool_call_sound` | `typing`, `elevator1-4` | - |
| `disable_interruptions` | true/false | false |
| `force_pre_tool_speech` | true/false | false |

---

## Verfügbare LLMs

| Model ID | Provider | Empfehlung |
|----------|----------|------------|
| `gpt-4.1` | OpenAI | **STANDARD** |
| `gpt-4.1-mini` | OpenAI | Budget |
| `gpt-4.1-nano` | OpenAI | FAQ-Bots |
| `gpt-4o` | OpenAI | Alternative |
| `gemini-2.5-flash` | Google | Niedrigste Latenz |
| `gemini-2.0-flash` | Google | Schnell |
| `claude-sonnet-4-5` | Anthropic | Premium |
| `claude-3-5-sonnet` | Anthropic | Reasoning |
| `custom-llm` | Custom | Eigener Endpoint |

---

## TTS-Modelle

| Model ID | Empfehlung |
|----------|------------|
| `eleven_v3_conversational` | **Standard** (75+ Sprachen) |
| `eleven_turbo_v2_5` | Schnell (32 Sprachen) |
| `eleven_flash_v2_5` | Schnellstes |
