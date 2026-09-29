# Develop Software with the Mavis Camera API

This guide explains how to use an AI coding agent to develop software that integrates with Mavis Camera. The agent connects to the camera, requests its live API schema, and uses that schema to understand the commands and state available on the connected device.

The live schema is authoritative. The agent must not invent or rely on a separately memorised list of API commands.

## Requirements

The AI coding agent must run on a computer that:

- Is connected to the same local network as the iPhone running Mavis Camera.
- Can open WebSocket connections to other devices on that network.
- Can create and run software locally.

A cloud-hosted coding agent will normally be unable to reach an iPhone at a private local-network address such as `192.168.x.x`, or `10.x.x.x`.

Keep Mavis Camera open in the foreground while developing and testing the integration.

## Get the WebSocket URL

On the iPhone:

1. Open **Settings** in Mavis Camera.
2. Open **API**.
3. Turn on **Enable API Access**.
4. If **Use API Keys** is enabled, create or open an enabled key.
5. Copy the **WebSocket URL** beginning with `ws://`.
6. Paste the complete URL into the AI chat when the agent asks for it.

The WebSocket URL may contain an API token. Treat the complete URL as a secret. Do not publish it or store it in source control.

## Instructions for the AI Coding Agent

When developing an integration with Mavis Camera, follow these instructions.

### Ask for the connection details

Ask the user for the complete Mavis Camera WebSocket URL beginning with `ws://`. Do not ask the user to extract the hostname, port, or token separately.

Confirm that your execution environment is running on the same local network as the iPhone and can reach its local-network address. If you are running in a hosted or cloud environment, explain that a local coding environment is required.

Treat the URL and any embedded token as a secret:

- Do not commit it to source control.
- Do not embed it in application source code.
- Do not place it in documentation, screenshots, fixtures, or published logs.
- Redact it from responses and diagnostic output.
- Accept it through user input, local configuration, or an environment variable in the software being developed.

### Connect and discover the API

Use an appropriate WebSocket library for the project's existing language and architecture. Connect to the exact URL supplied by the user and exchange WebSocket text messages.

Immediately after connecting, send this discovery request:

```json
{
  "enumerateSchema": {
    "mode": "ai",
    "includeDynamicConstraints": true,
    "replyTo": "initial-schema"
  }
}
```

Receive and retain the complete discovery response. Treat it as the authority for the connected camera's API, including:

- Supported operations.
- Command groups and templates.
- Argument names, order, and types.
- Numeric ranges, steps, units, and allowed values.
- Canonical state paths.
- Whether each state path can be queried or subscribed to.
- Reusable values and published type information.

Do not invent commands, arguments, state paths, value shapes, constraints, or capabilities that are absent from the live schema.

### Develop the requested software

Use the discovered schema to design and implement the integration requested by the user. Follow the project's existing architecture and conventions rather than creating an unrelated framework solely for the API.

Each WebSocket request is a JSON object containing one supported operation. Use command templates and argument metadata from the schema to construct command strings.

Use:

- `call` to execute a command.
- `query` for a one-time state read.
- `registerState` to receive the current state and subsequent changes.
- `unregisterState` to remove selected registrations.
- `clearRegisteredState` to remove all registrations for the current session.

Use a unique `replyTo` value when more than one direct request may be in flight. Match direct responses using that value. Unsolicited subscribed-state updates can arrive between direct responses and do not contain `replyTo`.

Use the exact canonical state-path spelling returned by discovery. Do not assume that command roots and state roots use identical capitalisation.

A successful command acknowledgement means the command was accepted. It does not prove that the observable camera state reached the requested value. Query or subscribe to the related state when confirmation is required.

Prefer deterministic commands such as `start` and `stop` over commands such as `toggle` when the required final state matters.

### Test safely

Schema discovery and state queries are appropriate initial connectivity tests. Do not send state-changing commands merely to prove that the connection works.

Before performing live tests that change camera settings, recording, tally, color grading, or other operational state, tell the user what will change and obtain their approval unless they have already explicitly authorised that test.

Use the narrowest useful live test for the requested integration, then confirm the result through the corresponding state path when one is available.

### Handle reconnection

Subscriptions belong to one WebSocket session. After reconnecting:

1. Request the live schema again.
2. Revalidate any commands and constraints used by the integration.
3. Restore the required state registrations.

Do not assume that a schema or subscription set from a previous connection remains current.

## Connection Troubleshooting

If the agent cannot connect, check that:

- Mavis Camera is open in the foreground.
- **Enable API Access** remains on.
- The iPhone and the agent's computer are on the same reachable local network.
- The user supplied the complete current URL beginning with `ws://`.
- Any API key contained in the URL remains enabled.
- The agent is running locally rather than in a hosted environment that cannot access the private network.

If the URL may be stale, ask the user to copy it from Mavis Camera again.
