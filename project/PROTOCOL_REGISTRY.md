# Protocol Registry

## Status

Accepted baseline for the shared WebSocket + Protobuf transport envelope.

## Wire-size principle

Protobuf binary payloads transmit numeric field tags and values, not source field names. Therefore abbreviating `messageId` to `mi` does not materially reduce the WebSocket wire payload. Wire-size optimization must prioritize:
- low field numbers for hot envelope fields (1-15),
- compact numeric message IDs rather than route strings,
- varint-friendly integer values,
- omission of default/unused fields,
- connection-scoped session/version/auth state instead of repeating it on every packet,
- avoiding unnecessary timestamps and duplicated metadata.

Short aliases are still standardized below for generated code/log/debug consistency and to avoid collisions.

## Envelope

```proto
message E {
  MT mt = 1;      // messageType
  uint32 mi = 2;  // messageId
  uint32 ri = 3;  // requestId / correlationId
  uint32 sq = 4;  // sequence, only when required
  uint32 ec = 5;  // errorCode; 0 means success and is normally omitted on wire
  bytes p = 6;    // payload: encoded business protobuf message selected by mi
}

enum MT {
  MT_UNSPECIFIED = 0;
  MT_REQ = 1;
  MT_RES = 2;
  MT_PUSH = 3;
  MT_STREAM = 4;
  MT_PING = 5;
  MT_PONG = 6;
}
```

Rules:
- `ri` is required for request/response correlation, but omitted/defaulted for pushes where no correlation is needed.
- `sq` is only used for streams, replay-sensitive flows, or features that explicitly require ordering; do not add sequence overhead to every business message without need.
- `ec` is only meaningful on responses/errors; success value `0` is not serialized by normal proto3 encoding.
- Session/auth/protocol version are negotiated/bound during connection handshake and are not repeated on every normal packet.
- Clock sync/latency timestamps belong in explicit ping/pong or time-sync messages rather than every envelope.
- `p` contains the serialized business message selected by `mi`; business modules must not send ad-hoc JSON inside `p`.

## Abbreviation Registry

| Alias | Canonical meaning | Notes |
|---|---|---|
| `mt` | messageType | Envelope message kind |
| `mi` | messageId | Numeric protocol message identifier |
| `ri` | requestId | Request/response correlation identifier |
| `sq` | sequence | Optional ordering/replay sequence |
| `ec` | errorCode | Numeric error code |
| `p` | payload | Serialized protobuf business payload |
| `uid` | userId | Only where a user identifier is genuinely part of a message |
| `zid` | zoneId | Only where cross-zone identity is required; do not repeat on normal zone-bound traffic |
| `ts` | timestamp | Use only in explicit messages that need time data |

New aliases must be added here before use. If an alias conflicts, extend it by one or two meaningful characters rather than assigning the same alias to multiple meanings.

## Message ID Registry Rules

- `mi` is numeric; route strings are never transmitted in normal production packets.
- A generated registry in `packages/protocol/` will map `mi <-> protobuf message/route` for Client, Server, Network Lab and logs.
- IDs are never reused for a different semantic after release.
- Prefer small IDs for high-frequency/core messages because protobuf encodes small unsigned integers more compactly.
- Reserve `1-31` for core network/session/control messages.
- Reserve `32-127` preferentially for high-frequency realtime messages.
- Normal business messages may use IDs `>=128`; allocation must be recorded centrally rather than invented inside feature modules.
