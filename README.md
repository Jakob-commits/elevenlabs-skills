# ElevenLabs Skills für Claude Code

Claude-Code-Skills + MCP-Setup für die Arbeit mit **ElevenLabs Conversational AI** (Voice Agents, Webhooks, Tools, Outbound Calls).

## Inhalt

- **`skills/elevenlabs-agent-management/`** — Skill mit Best Practices für Agent-Erstellung, LLM-Konfiguration, Webhook-Setup, Tools, Voice-Auswahl und Knowledge-Base-Management.
- **`.mcp.json.example`** — MCP-Server-Config-Template für den offiziellen ElevenLabs-MCP-Server.

## Schnellstart

### 1. Repo klonen

```bash
git clone https://github.com/Jakob-commits/elevenlabs-skills.git
cd elevenlabs-skills
```

### 2. MCP-Config einrichten

```bash
cp .mcp.json.example .mcp.json
```

Dann `.mcp.json` öffnen und deinen **ElevenLabs API-Key** eintragen:

```json
{
  "mcpServers": {
    "elevenlabs": {
      "command": "uvx",
      "args": ["elevenlabs-mcp"],
      "env": {
        "ELEVENLABS_API_KEY": "sk-deinen-key-hier-eintragen"
      }
    }
  }
}
```

API-Key holen unter: https://elevenlabs.io/app/settings/api-keys

### 3. Voraussetzungen

- **Claude Code** installiert (https://claude.com/claude-code)
- **`uvx`** verfügbar (kommt mit `uv` — Installation: https://docs.astral.sh/uv/)
- Aktiver ElevenLabs-Account mit Conversational-AI-Zugriff

### 4. Claude Code starten

```bash
claude
```

Claude Code lädt automatisch:
- den **Skill** aus `skills/elevenlabs-agent-management/`
- den **MCP-Server** aus `.mcp.json`

Ab dann kannst du Sachen sagen wie:
- *"Erstelle einen ElevenLabs Voice Agent für Kundenservice auf Deutsch."*
- *"Liste alle meine Agents auf und zeig mir die Conversations vom letzten Tag."*
- *"Füge dem Agent XYZ ein Webhook-Tool für Termin-Buchung hinzu."*

## Was der Skill abdeckt

- ✅ Agent erstellen (MCP) und aktualisieren (REST-API, da PATCH kein MCP-Tool)
- ✅ LLM-Konfiguration (gpt-4.1, gemini, claude — Standard-Empfehlungen)
- ✅ TTS-Modelle (`eleven_v3_conversational`, `eleven_turbo_v2_5`, `eleven_flash_v2_5`)
- ✅ Webhooks (Conversation Initiation, Post-Call)
- ✅ Server Tools (webhook, system, client, mcp) inkl. häufige Fallen
- ✅ Voice Library + Voice Cloning
- ✅ Outbound Calls + Phone-Number-Management
- ✅ Knowledge Base, Conversations, Transcripts

## MCP-Tools (per ElevenLabs MCP verfügbar)

Nach Setup verfügbar in Claude Code:
- `create_agent`, `get_agent`, `list_agents`
- `add_knowledge_base_to_agent`
- `make_outbound_call`, `list_phone_numbers`
- `list_conversations`, `get_conversation`
- `search_voices`, `search_voice_library`, `voice_clone`, `text_to_voice`
- `text_to_speech`, `speech_to_speech`, `speech_to_text`
- `compose_music`, `text_to_sound_effects`, `isolate_audio`
- `check_subscription`, `list_models`

## Für Claude.ai Web (ohne Claude Code)

Die SKILL-Markdown-Datei kann direkt in Claude.ai als Custom Skill hochgeladen werden — der Skill funktioniert dann mit dem Claude.ai-eigenen ElevenLabs-Connector ohne lokales MCP-Setup.

## Lizenz

MIT — siehe [LICENSE](LICENSE).
