# Public Game Configuration

Human-maintained source tables use `.xlsx` under `source/`. Data dictionaries/validation contracts live under `schema/`.

## Ownership
- Product owns the business structure of public configuration: table catalog, field meaning, tunable parameters, business IDs and relationships, balancing goals, numeric content, source workbooks and data dictionaries.
- Tech Lead reviews field types, stable IDs/reference integrity, executable constraints, Client/Server visibility and compatibility, and owns generation and technical validation rules. Tech Lead does not change product meaning unilaterally.
- Client/Server consume approved, validated outputs; they do not invent independent config semantics.

## Per-Table Data Dictionary
For each table document:
- unique ID
- field/stable abbreviation where useful
- type
- business meaning
- default/null policy
- enum/range/unique rules
- references/FKs
- Client-only / Server-only / Shared
- hot-update eligibility if later supported
- compatibility/deprecation policy

## Validation
At minimum validate unique IDs, references, types/ranges, required values, deprecated ID/field reuse, and accidental exposure of sensitive server-only fields to Client.

Do not create speculative tables just because the template exists. Add actual `.xlsx` files only when the project has a real requirement.
