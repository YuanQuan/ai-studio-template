# Redis Key Convention — Standard

Status: **ACCEPTED**

## Goals

- Short keys to reduce Redis memory overhead.
- Stable, centrally registered abbreviations.
- Clear module ownership.
- No zone prefix in normal production keys when each zone already uses an isolated Redis datasource.
- Easy future migration of an entire zone by changing datasource configuration rather than rewriting keys.

## Base Pattern

```text
<ns>:<id>[:<sub>[:<subId>]]
```

Rules:
- lowercase ASCII only.
- `:` is the only segment separator.
- do not include environment or zone id when the Redis datasource is already isolated by environment/zone.
- never put user-controlled free text directly into keys; use numeric/internal IDs or a safe digest.
- new namespace abbreviations must be registered here before use.

## Namespace Registry

| Prefix | Meaning | Typical structure |
|---|---|---|
| `u` | user/player cached data | `u:<uid>:<sub>` |
| `ss` | session | `ss:<sid>` |
| `on` | online presence | `on:<uid>` |
| `rk` | ranking | `rk:<rankId>` |
| `cd` | cooldown/temporary expiry state | `cd:<type>:<id>` |
| `tm` | timer/schedule acceleration data | `tm:<type>` |
| `rl` | rate limit | `rl:<scope>:<id>` |
| `lk` | distributed/coordination lock, only when truly required | `lk:<scope>:<id>` |
| `ct` | counter | `ct:<type>:<id>` |
| `cf` | runtime/config cache | `cf:<name>` |

## User Sub-key Registry

| Sub | Meaning | Example |
|---|---|---|
| `b` | basic/profile cache | `u:123:b` |
| `inv` | inventory cache | `u:123:inv` |
| `q` | quest cache/runtime | `u:123:q` |
| `act` | activity cache/runtime | `u:123:act` |
| `eq` | equipment cache | `u:123:eq` |

Do not create a new short token if an existing registered token has the same meaning. If a collision occurs, extend by 1-2 meaningful letters and register it.

## Examples

```text
u:123:b                # player 123 basic cache
u:123:inv              # player 123 inventory cache
ss:8f31ac               # session
on:123                  # online presence
rk:arena:d              # daily arena ranking
cd:skill:123:501        # user/entity cooldown example
tm:daily                # timer schedule index
rl:login:123            # login rate-limit bucket
ct:mail:123             # mail-related counter
```

## TTL Rules

TTL is appropriate for:
- sessions,
- presence leases,
- cache expiry,
- cooldown/temporary state,
- rate-limit buckets,
- disposable locks with safety timeout.

TTL/key expiration must **not** be the only trigger for business-critical scheduled jobs. Reliable timers should keep recoverable schedule state and use a due-time index such as a Redis Sorted Set and/or MySQL task record.

## Zone Isolation

Preferred production model:
- one zone -> one configured MySQL logical database/schema datasource,
- one zone -> one configured Redis datasource,
- application code receives datasource through zone configuration/DI.

Multiple zones may share a physical database/Redis host initially if operationally necessary, but logical ownership must remain separable so a single zone can later migrate to another physical instance without changing business code.

For Redis specifically, prefer an independently addressable datasource/instance (or cluster namespace) per zone over relying on Redis `SELECT dbNumber` as the primary long-term isolation mechanism, because independent endpoints make migration, monitoring, capacity planning and failure isolation clearer.
