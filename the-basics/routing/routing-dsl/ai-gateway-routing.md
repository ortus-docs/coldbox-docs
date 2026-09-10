---
description: >-
  Expose a BoxLang AI Gateway over HTTP with toAiGateway() - inbound platform
  events, URL verification handshakes, and human-in-the-loop approvals.
---

# AI Gateway Routing

{% hint style="warning" %}
AI Gateway Routing requires **BoxLang** and the **bx-ai** module. It is not available on CFML engines.
{% endhint %}

A **gateway** (bx-ai's `IGateway`) is a bidirectional adapter between an agent and a platform: Slack, Telegram, WhatsApp, a signed webhook of your own. It turns a platform's inbound payload into a normalized message, and turns the agent's output back into something that platform understands.

`toAiGateway()` puts that surface on a route. One declaration registers everything a platform needs to talk to your application:

```javascript
route( "/gateways" ).toAiGateway( session: "SupportAgentSession" );
```

Like `resources()`, `toAi()` and `toMCP()`, a single declaration expands into several concrete routes, and every modifier already on the route (`.withSSL()`, `.withDomain()`, `.withCondition()`, `.as()`, `.withModule()`) is inherited by all of them.

## The Routes It Registers

| HTTP Verb   | Pattern                                        | Purpose                                          |
| ----------- | ---------------------------------------------- | ------------------------------------------------ |
| `POST`      | `{pattern}[/:gateway]/events`                  | An inbound platform event                        |
| `GET`       | `{pattern}[/:gateway]/events`                  | The platform's URL verification handshake        |
| `GET`       | `{pattern}/interactions/:requestID`            | Poll a pending human-in-the-loop interaction     |
| `POST`      | `{pattern}/interactions/:requestID/decisions`  | Submit a human's decision                        |
| `GET`       | `{pattern}/info`                               | What this mount serves                           |

`GET` and `POST` deliberately share the `/events` path. A platform is given **one** URL to store, and most verify it with a `GET` before they will ever `POST` anything to it - Meta's `hub.challenge` echo being the canonical example. One URL, both roles.

Route names follow the base route's name (or its pattern): `{base}.gateway.events`, `{base}.gateway.interaction`, `{base}.gateway.decision`, `{base}.gateway.info`.

## Arguments

```javascript
route( pattern ).toAiGateway( [ gateway ], [ session ] )
```

| Argument  | Type             | Description                                                                                                                |
| --------- | ---------------- | -------------------------------------------------------------------------------------------------------------------------- |
| `gateway` | string           | The registered gateway name to pin this mount to. Defaults to empty, which mounts a `:gateway` placeholder serving every gateway in `aiGatewayRegistry()` |
| `session` | string or object | A WireBox ID (resolved on every request) or a live `GatewaySession`. Defaults to empty, which parses inbound events without dispatching them |

## One Mount, Every Gateway

Leave the gateway name out and the terminator inserts its own `:gateway` placeholder, so a single mount serves everything registered in `aiGatewayRegistry()`:

```javascript
// config/Router.bx
function configure(){

    route( "/gateways" )
        .withSSL()
        .toAiGateway( session: "SupportAgentSession" );

}
```

```
POST /gateways/slack/events      → the "slack" gateway
POST /gateways/telegram/events   → the "telegram" gateway
GET  /gateways/info              → every registered gateway and its capabilities
```

## Pinned To One Gateway

Pass a name and the mount is fixed to that gateway, with no placeholder in the URL:

```javascript
route( "/webhooks/slack" ).toAiGateway( "slack", "SupportAgentSession" );
```

```
POST /webhooks/slack/events
GET  /webhooks/slack/events
```

A pinned name always wins over anything in the request collection, so a request can never redirect a pinned mount at a different gateway.

## Dispatching To An Agent

Pass a `session` and every message parsed out of an inbound event is dispatched as an agent turn, and the request is acked **`202` immediately** - the turn is never waited on. This is deliberate: a platform webhook times out in seconds, while an agent turn takes as long as it takes.

```json
{
    "accepted" : 1,
    "messages" : [ { "id" : "...", "threadId" : "slack:C1234" } ]
}
```

The thread each message landed on comes back in the body, and single-message events also echo it as an `X-Thread-Id` response header - so you can correlate the reply that the gateway delivers later.

Omit the `session` and nothing is dispatched: the event is verified, parsed, and returned as `200` with the normalized messages, for your own code to handle.

```javascript
// Verify and parse only - no agent is called
route( "/gateways" ).toAiGateway();
```

## Wiring The Session

The `session` argument is a WireBox ID resolved on every request, so registering the route never forces the session to be constructed. Build it in a factory model and map it as a singleton:

```javascript
// models/GatewaySessionFactory.bx
class {

    property name="supportAgent" inject="SupportAgent";

    function build(){
        return aiGatewaySession(
            agent   : variables.supportAgent,
            gateways: [ aiGatewayRegistry().get( "slack" ) ],
            policy  : "queue"
        ).start();
    }

}
```

```javascript
// config/WireBox.bx
function configure(){

    map( "SupportAgentSession" )
        .toFactoryMethod( factory: "GatewaySessionFactory", method: "build" )
        .asSingleton();

}
```

The gateways themselves are registered with bx-ai at startup, not by ColdBox - a module's `onLoad()` or an `afterAspectsLoad()` interceptor is a good home:

```javascript
aiGatewayRegistry().register(
    aiGateway( "http", { secret: getSystemSetting( "GATEWAY_SECRET" ) } )
);
```

A live session object works too, if you already have one:

```javascript
route( "/gateways" ).toAiGateway( session: application.supportSession );
```

## Security

Every inbound event is handed to the gateway's own `verifyInbound()` **before** it is parsed or dispatched, and a decision submission is verified against the gateway's signing scheme. Each gateway owns its scheme - bx-ai's `http` gateway uses HMAC-SHA256 with nonce dedup and a timestamp tolerance; a platform gateway uses whatever that platform sends.

| Status | Meaning                                                         |
| ------ | ---------------------------------------------------------------- |
| `202`  | Accepted and dispatched                                          |
| `200`  | Verified and parsed (no session), or an interaction read/decided |
| `400`  | The body was not valid JSON                                      |
| `401`  | Signature verification failed                                    |
| `404`  | Unknown gateway, or unknown interaction                          |
| `405`  | The gateway does not support a verification handshake            |
| `409`  | That interaction was already resolved                            |
| `410`  | That interaction expired                                         |

Since these endpoints are internet-facing by definition, pair them with `.withSSL()`, and keep secrets in environment variables rather than in your Router.

## Human-In-The-Loop

When an agent suspends for human approval, the interaction endpoints are how a human answers it:

```
GET  /gateways/interactions/{requestID}            → the pending question and its allowed decisions
POST /gateways/interactions/{requestID}/decisions  → { "decision": "approve", "reason": "looks fine" }
```

The decision request must be signed the same way an inbound event is. Both endpoints live under the mount's base path, not under a gateway name: an interaction is resolved by its id across every registered gateway.

## Modifier Inheritance

```javascript
route( "/gateways" )
    .withSSL()
    .withDomain( "hooks.myapp.com" )
    .withCondition( ( route, params, event ) => !maintenanceMode )
    .toAiGateway( session: "SupportAgentSession" );
```

Every generated sub-route carries all three.

## Choosing A Terminator

| Terminator      | Use it for                                                                           |
| --------------- | ------------------------------------------------------------------------------------ |
| `toAi()`        | Your own HTTP API in front of an agent - you control the client                       |
| `toMCP()`       | Exposing tools and data to MCP clients over the Model Context Protocol                |
| `toAiGateway()` | A platform talking to your agent on the platform's terms - webhooks, signatures, HITL |

They compose freely:

```javascript
// config/Router.bx
function configure(){

    route( "/api/chat" ).withSSL().toAi( "models.ChatAgent" );
    route( "/mcp/:mcpServer" ).withSSL().toMCP();
    route( "/gateways" ).withSSL().toAiGateway( session: "SupportAgentSession" );

}
```

## Learn More

* [AI & MCP Routing](ai-routing.md) - the `toAi()` and `toMCP()` terminators
* [Agentic ColdBox](../../../digging-deeper/ai/agentic-coldbox.md) - building agents in a ColdBox app
* [BoxLang AI documentation](https://ai.ortusbooks.com/) - gateways, sessions, and the `IGateway` interface
