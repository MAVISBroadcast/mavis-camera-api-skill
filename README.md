# Mavis Camera API skill

An [Agent Skill](https://agentskills.io/) for building software that integrates with Mavis Camera. [SKILL.md](SKILL.md) tells the agent to follow the bundled [Mavis Camera AI Developer Guide](Mavis-Camera-AI-Developer-Guide.md), connect over WebSocket, and use the connected camera's live API schema to develop and test your integration.

For an overview of the camera API, see [Mavis Camera API Introduction](https://support.mavis.cloud/hc/en-us/articles/48836696262289-API-Introduction).

## Quicker start: use the guide directly

You can use the guide without installing a skill. [Download the Mavis Camera AI Developer Guide](https://raw.githubusercontent.com/MAVISBroadcast/mavis-camera-api-skill/main/Mavis-Camera-AI-Developer-Guide.md), attach the Markdown file to a chat with your coding agent, and use this prompt:

> I want to develop software that integrates with Mavis Camera. Read the attached Mavis Camera AI Developer Guide and follow it. Start by asking me what I want to build and for the `ws://` WebSocket URL. Then connect to the camera, discover its live API schema, and develop the integration.

## Requirements

Run your coding agent locally on a computer that can reach the iPhone running Mavis Camera over the same network. Keep the app open in the foreground and enable API access in **Mavis Camera → Settings → API**. A cloud-hosted agent generally cannot connect to a camera at a private local-network address.

The WebSocket URL may contain an API token. Treat the entire URL as a secret; do not add it to your code or commit it to a repository.

## Use

If you've added this repository to your coding agent's skills, try:

> I want to develop software that integrates with Mavis Camera. Use the mavis-camera-api-skill. Start by asking me what I want to build and for the `ws://` WebSocket URL. Then connect to the camera, discover its live API schema, and develop the integration.

Find the complete WebSocket URL in **Mavis Camera → Settings → API** after enabling API access. Supply it in the chat when asked. The agent will use the live schema for the connected camera rather than assuming a fixed set of commands.

## License

MIT. See [LICENSE](LICENSE).
