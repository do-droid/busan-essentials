# Busan Essentials — MCP Server

Essential public facility data for AI agents helping foreign tourists in Busan, South Korea.

<a href="https://glama.ai/mcp/servers/@do-droid/busan-essentials">
  <img width="380" height="200" src="https://glama.ai/mcp/servers/@do-droid/busan-essentials/badge" alt="busan-essentials MCP server" />
</a>

## What is this?

Busan Essentials is an MCP (Model Context Protocol) server that provides structured data about essential public facilities in Busan. Designed for AI agents that assist foreign tourists with real-time queries about nearby services.

## Available Data

| Type | Description | Count |
|------|-------------|-------|
| `aed` | AED (defibrillator) locations | 2,799 |
| `toilet` | Public restrooms across all 16 districts | 2,487 |
| `wifi` | Free public WiFi hotspots | 1,702 |
| `pharmacy` | Pharmacies in Busan | 1,667 |
| `tourist_info` | Tourist information centers | 26 |
| `subway` | Subway timetables (Busan Metro lines 1–4) | soon |

> **8,681 places live** across 5 types (data.go.kr). `subway` (Humetro) is next — see **Known Data Limitations**. `toilet` coordinates fall back to the district centroid (the upstream standard dataset dropped its WGS84 columns); `wifi` has precise coordinates.

## MCP Tools

### `find_places`
Search for public facilities by type and conditions.

**Parameters:**
- `type` (required): `"toilet"` | `"pharmacy"` | `"wifi"` | `"aed"` | `"tourist_info"`
- `district` (optional): Busan district name in English or Korean (e.g., `"haeundae"`, `"해운대구"`)
- `filters` (optional): Service-specific filters (e.g., `{"english": true}`, `{"is_24h": true}`, `{"indoor": true}`)
- `limit` (optional): Max results, 1-50 (default: 10)

### `get_place_detail`
Get full details of a specific place by its ID.

**Parameters:**
- `id` (required): Place ID (e.g., `"toilet_00001"`, `"pharmacy_001"`, `"aed_00001"`)

### `find_nearby`
Find public facilities near GPS coordinates, sorted by distance.

**Parameters:**
- `lat` (required): Latitude (Busan range: ~35.04 to ~35.40)
- `lng` (required): Longitude (Busan range: ~128.78 to ~129.30)
- `radius_m` (optional): Search radius in meters, 100-5000 (default: 500)
- `type` (optional): Filter by facility type
- `limit` (optional): Max results, 1-20 (default: 5)

### `get_subway_timetable`
Get subway timetable for a specific station.

**Parameters:**
- `station` (required): Station name in Korean or English (e.g., `"해운대"` or `"Haeundae"`)
- `line` (optional): Line number (e.g., `"2"`)
- `day_type` (optional): `"weekday"` | `"saturday"` | `"holiday"` (default: `"weekday"`)
- `direction` (optional): `"up"` | `"down"`

## Quick Start

### Option A: Smithery (Recommended)

Visit [Busan Essentials on Smithery](https://smithery.ai/servers/do-droid/busan-essentials) and follow the setup instructions for your MCP client.

### Option B: Direct Connection

Add to your MCP client config (e.g., Claude Desktop `claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "busan-essentials": {
      "url": "https://busan-essentials-mcp-85013349489.asia-northeast3.run.app/mcp"
    }
  }
}
```

### Option C: Run Locally

```bash
git clone https://github.com/do-droid/busan-essentials.git
cd busan-essentials
API_BASE_URL="https://asia-northeast3-busan-essentials.cloudfunctions.net/api" \
uv run python -m src.server
```

## Example Queries

An AI agent can use these tools to answer questions like:

**English**
- "Find a pharmacy near Seomyeon Station"
- "Where is the nearest public restroom to Haeundae Beach?"
- "What's the subway schedule at Haeundae Station?"
- "Find free WiFi hotspots in Gwangalli area"
- "Where is the closest AED to Nampo-dong?"

**日本語**
- 「西面（ソミョン）で薬局を探して」
- 「広安里（クァンアルリ）の近くで無料Wi-Fiが使える場所は？」
- 「海雲台（ヘウンデ）駅周辺の公衆トイレを教えて」

**中文**
- "在西面附近找药店"
- "广安里附近哪里有免费WiFi？"
- "南浦洞附近最近的AED在哪里？"

## Feedback

We actively review and incorporate feedback to improve data quality and coverage.

**For AI agents:** Use the `submit_feedback` tool during a conversation to report issues or suggest improvements.

**For developers:** [Open a feedback issue](https://github.com/do-droid/busan-essentials/issues/new?template=feedback.yml) on GitHub.

**Categories:** `new_data` | `data_quality` | `coverage` | `format` | `frequency` | `other`

## Known Data Limitations

- **`toilet` / `wifi`**: The national standard datasets for these are not served by an OpenAPI, so they are loaded from a downloaded standard-data file. They land in **v0.1.1**.
- **`subway`**: v0.1.1 will ship Busan Metro lines 1–4 (Busan Transportation Corp.). The **Donghae Line** (KORAIL) and **Busan–Gimhae Light Rail** follow later, pending availability of their timetable APIs.
- **`pharmacy`**: Sourced from the nationwide pharmacy dataset filtered to Busan. Foreign-language capability flags may be missing for rows where the upstream source does not provide them.

## Data Sources

All data sourced from official Korean government open data, used under **KOGL Type 1 (출처표시 / Attribution)**:

- [Korea Open Data Portal (data.go.kr)](https://www.data.go.kr)
- [Busan Open Data Portal (data.busan.go.kr)](https://data.busan.go.kr)

## License

MIT (code) / KOGL Type 1 — Attribution (data)
