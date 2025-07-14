# VoiceAgent Sample Implementation


This repository initially started as a 'Fork' to [AudioConnectorBluePrint](https://github.com/GenesysCloudBlueprints/audioconnector-server-reference-implementation) and updated/refactored it for Showcase Sample Integration with Voice AI Agent Platforms.
### Purpose
This repository contains a sample implementation for VoiceAgent based on AudioConnector Server. This can be used as a guide to help understand some of the basics of setting up an AudioConnector Server and how it can be used to develop an integration with VoiceAgent Platforms. 
It is not intended for production purposes. Protocol documentation can be found on the [Genesys Developer Portal](https://developer.genesys.cloud/devapps/audiohook/).


## Project Setup & Installation

### Prerequisites
- Node.js version **18 or higher** is required.
- TypeScript is used for development.
- `ts-node` is used for development-time execution.

### Available Scripts
npm start - Starts the application using ts-node.
npm run build - Cleans and builds the project.
npm run clean - Removes all compiled files in dist/.
npm test - Placeholder for future test implementation.

### 📦 Notable Dependencies

- express: Web server for handling HTTP requests.
- websocket / ws: WebSocket servers and clients.
- uuid: For generating unique IDs.
- dotenv: Loads environment variables from a .env file.
- axios: HTTP client for API requests.
- lodash: Utility functions for data manipulation.
- iso8601-duration: Parses ISO8601 time durations.


### Running the server

#### Requirements
This implementation was written using NodeJS 18.16.0 as a target. If you are using a Node version manager, there is a [nvmrc](./.nvmrc) file that specifies this version.

#### Steps to run the server locally
1) Run `npm install` in the root of the project.
2) Run `npm run start` in the root of the project to start the server. The port can be adjusted from within the [environment](./.env) file.


### Genesys Cloud Pre-requisites

- Setup Audio connector Integration in your Environment [AudioConnector](https://help.mypurecloud.com/articles/audio-connector-overview/)

- In the Architect Flow invoke the Call Audio Connector Block Pass all variables referred to in the Prompt - including Prompt Name
![Call Audio Connector](./AudioConnectorIntegration.jpg)

The above screenshot assume we have NewBookingPrompt and corresponding Toolset created under the prompts folder (see below)
### Setting up Voice Agent Prompt
Create a [Prompt](./src/prompts/NewBookingPrompt.md) and Corresponding [Tool](./src/prompts/NewBookingTools.json)


### External Dependencies
- Setup an OpenAI API Key/Subscription
- Voice Agent Integration is with [Open AI Realtime API](https://platform.openai.com/docs/api-reference/realtime)
- Setup NGrok if require testing from local PC



### Development
- typescript, ts-node: TypeScript tooling for compiling and executing code.
- rimraf: Cross-platform rm -rf equivalent for cleaning directories.
- @types/*: Type definitions for Express, Node.js, WebSocket, and other libraries.


#### Main Classes
The [Session](./src/websocket/session.ts) class contains methods and logic that handle communicating with the AudioConnector Client (which is Genesys Cloud).


The most important aspect in the Session class is to review the how Audio is sent to Genesys Cloud In order to prevent Rate Limit violations.
Audio Connector requires Audio to be sent at the Sampling Rate of the Audio Format of the Session
When the Voice Agent Platform streams audio faster then the Sampling Rate - the Audio Connector Buffers the Audio.

The session object has an instance of 
The [VoiceAIAgentBaseClass](./src/services/voice-aiagent-base.ts) is the base class for all VoiceAI Agent Platform - which is the base class for all VoiceAI Agent Platforms.
Currently we have 2 sample implementations
- [OpenAIRealTime](./src/services/open-ai.ts)
- [DeepgramAIVoiceAgent](./src/services/deepgram.ts)

Any new VoiceAI Agent API Platform we need to integrate with must be inherited from the VoiceAIAgentBaseClass and we need to also update the [VoiceAIAgentFactory](./src/services/voice-aiagent-factory.ts) to instantiate a new VoiceAIAgentBaseClass from name.





The [SecretService](./src/services/secret-service.ts) class is responsible for looking up the secret from a given API Key used during the initial authentication process. A fake implementation has been provided, and will need to be replaced to lookup secrets with whatever service they are stored in.
