# VoiceAgent Sample Implementation

This repository started as a fork of [AudioConnectorBluePrint](https://github.com/GenesysCloudBlueprints/audioconnector-server-reference-implementation) and has been updated/refactored to serve as a showcase sample integration with Voice AI Agent platforms.

---

## 🗭 Purpose

This repository provides a sample implementation for a Voice Agent based on the AudioConnector Server. It is intended to help users understand the basics of setting up an AudioConnector Server and how to integrate it with Voice Agent platforms.

> **Note:** This is not intended for production use. Protocol documentation is available on the [Genesys Developer Portal](https://developer.genesys.cloud/devapps/audiohook/).

---

## ⚙️ Project Setup & Installation

### Prerequisites

- Node.js **version 18 or higher**.
- TypeScript for development.
- `ts-node` for development-time execution.

### Available Scripts

```bash
npm start         # Starts the application using ts-node
npm run build     # Cleans and builds the project
npm run clean     # Removes all compiled files in dist/
npm test          # Placeholder for future test implementation
```

### 📦 Notable Dependencies

- `express`: Web server for handling HTTP requests
- `websocket`, `ws`: WebSocket servers and clients
- `uuid`: Generates unique IDs
- `dotenv`: Loads environment variables from a `.env` file
- `axios`: HTTP client for API requests
- `lodash`: Utility library for data manipulation
- `iso8601-duration`: Parses ISO8601 duration strings

---

## ▶️ Running the Server

### Node Version

This project was written for Node.js `18.16.0`. If using a Node version manager, the project includes an [`.nvmrc`](./.nvmrc) file specifying this version.

### Steps to Run Locally

1. Install dependencies:
   ```bash
   npm install
   ```
2. Start the server:
   ```bash
   npm run start
   ```
   You can configure the listening port via the [.env](./.env) file.

---

## 🎧 Genesys Cloud Prerequisites

- Configure the Audio Connector integration in your Genesys Cloud environment: [AudioConnector Documentation](https://help.mypurecloud.com/articles/audio-connector-overview/)

- In your **Architect Flow**, invoke the **Call Audio Connector** block and pass all required variables referenced in the prompt (including prompt name):



This example assumes a `NewBookingPrompt` and corresponding toolset have been created under the `prompts` folder (see below).

---

## 🗣️ Setting Up Voice Agent Prompt

- Create the [Prompt](./src/prompts/NewBookingPrompt.md)
- Define the corresponding [Tool](./src/prompts/NewBookingTools.json)

---

## 🌐 External Dependencies

- OpenAI API key/subscription for Voice Agent integration
- Voice Agent is integrated via the [OpenAI Realtime API](https://platform.openai.com/docs/api-reference/realtime)
- [NGrok](https://ngrok.com/) if testing locally

---

## 💠 Development Notes

- `typescript`, `ts-node`: Tooling for compiling and executing TypeScript
- `rimraf`: Cross-platform `rm -rf` equivalent for cleaning directories
- `@types/*`: Type definitions for Express, Node.js, WebSocket, etc.

---

## 🧹 Core Classes

### [`Session`](./src/websocket/session.ts)

Handles communication with the AudioConnector Client (Genesys Cloud).\
**Important:** It regulates how audio is sent to Genesys Cloud to prevent rate-limit violations.

> Audio must be streamed at the sampling rate defined by the session's audio format.\
> If the Voice Agent streams audio faster than this rate, the Audio Connector buffers the audio.

### [`VoiceAIAgentBaseClass`](./src/services/voice-aiagent-base.ts)

Base class for all Voice AI Agent platforms. New integrations should inherit from this class.

#### Sample Implementations:

- [`OpenAIRealTime`](./src/services/open-ai.ts)
- [`DeepgramAIVoiceAgent`](./src/services/deepgram.ts)

To integrate a new Voice AI platform:

- Inherit from `VoiceAIAgentBaseClass`
- Update the [VoiceAIAgentFactory](./src/services/voice-aiagent-factory.ts) to instantiate the new class.

### [`SecretService`](./src/services/secret-service.ts)

Looks up secrets (like API keys) during initial authentication.

> A mock implementation is included—replace with your preferred secret management system.

---

## 🐳 Docker Container

Two Dockerfiles are provided for building container images:

1. `Dockerfile`: For cloud-hosted environments like AWS EKS or Azure Container Services
2. `Dockerfile.proxy`: For local hosting with NGrok tunneling to the Audio Connector

### 🔧 Run Docker Container Locally with NGrok

1. Install Docker and ensure the daemon is running.
2. Build and run the container:

```bash
docker build -f Dockerfile.proxy -t audio-connector-tunnel .
docker run --dns 8.8.8.8 --env-file .env -p 8081:8081 audio-connector-tunnel
```

> This assumes the Audio Connector listens on port `8081`. DNS is set to Google DNS (8.8.8.8).
