# CTF Pilot's Challenge Schema

This schema defines the structure for describing CTF challenges. It ensures a unified format for challenge descriptions and includes all necessary information for automation.

Both YAML and JSON versions of this schema are used. This README documents every supported field and enumerates all allowed values so maintainers and challenge authors have a single reference.

Examples of the schema in use can be found in the [examples](./examples) directory.

## Quick reference

The schema is an object. At minimum a challenge SHOULD include the following fields:

- `enabled` (boolean) - whether the challenge is enabled. Default: `true`.
- `name` (string) - - human-readable name of the challenge.
- `slug` (string) - machine-friendly slug. Must match the regex `^[a-z0-9-]+$`.
- `author` (string) - author or owner of the challenge.
- `category` (string) - [see allowed values](#allowed-enumerations-and-constraints).
- `difficulty` (string) - [see allowed values](#allowed-enumerations-and-constraints).
- `tags` (array[string], optional) - freeform tags.
- `type` (string) - `static` | `shared` | `instanced`.
- `instanced_type` (string, optional) - `web` | `tcp` | `none` (useful for `instanced` challenges).
- `flag` (string | array | array of objects) - one or more flags (see Flags section).
- `points` (integer) - starting point value (1..10000). Default 1000.
- `min_points` (integer) - minimum point value (1..1000). Default 100.

See all value types in [Field reference](#field-reference-structure-and-examples).

## Allowed enumerations and constraints

These are the explicit allowed values used by the schema and tooling.

- `category` (one of):
  - `web`
  - `forensics`
  - `rev`
  - `crypto`
  - `pwn`
  - `boot2root`
  - `osint`
  - `misc`
  - `blockchain`
  - `mobile`
  - `test`

- `difficulty` (one of):
  - `beginner`
  - `easy`
  - `easy-medium`
  - `medium`
  - `medium-hard`
  - `hard`
  - `very-hard`
  - `insane`

- `type` (one of):
  - `static` - challenge is a static artifact (files, puzzles, etc.).
  - `shared` - external network service; may require a `connection` string. Instance is shared among all teams.
  - `instanced` - an instance-per-team/service is launched (see `instanced_type`).

- `instanced_type` (one of):
  - `web` - an HTTP/web instance.
  - `tcp` - a plain TCP/port-based instance.
  - `none` - not instanced / no special instance behavior.

- `tags`: each tag is a string and should match the regex: `^[a-zA-Z0-9-_:;? ]+$` (allows letters, numbers, hyphen, underscore, colon, semicolon, question mark and spaces).

- `slug`: must match `^[a-z0-9-]+$`, min length 1, max length typically 50 (see schema file for authoritative limits).

- `points`: integer between 1 and 10000 (inclusive). Default 1000.
- `min_points`: integer between 1 and 1000 (inclusive). Default 100.

## Field reference (structure and examples)

- `version` (string, optional) - schema version (e.g. `1.0.0` or `1`). Fully optional; if omitted the latest schema version is assumed.
- `enabled` (boolean) - whether the challenge is active.
- `name` (string) - e.g. "Demo Challenge".
- `slug` (string) - e.g. `demo-challenge`.
- `author` (string) - e.g. `The Mikkel`.
- `category` (string) - see enumerations above.
- `difficulty` (string) - see enumerations above.
- `tags` (array[string], optional) - e.g. `["demo", "example"]`.
- `type` (string) - `static`, `shared`, or `instanced`.
- `instanced_type` (string, optional) - `web`, `tcp`, or `none`.
- `instanced_name` (string, optional) - slug of the instanced challenge. If omitted the challenge `slug` is typically used.
- `instanced_subdomains` (array[string], optional) - list of subdomains (each matching `^[a-z0-9-]+$`) used for instanced web challenges.
- `connection` (string, optional) - a connection URI or host:port string for `shared` challenges (max length 255).
- `flag` (string | array | array of objects) - see [Flags section](#flags).
- `points` (integer, optional) - initial points awarded for solving.
- `min_points` (integer, optional) - floor for dynamic scoring.
- `decay` (integer, optional) - decay for dynamic scoring. Default: `75`.
- `description_location` (string, optional) - path to a file with the challenge description (e.g. `description.md`).
- `prerequisites` (array[string], optional) - other challenge slugs that must be solved first.
- `dockerfile_locations` (array[object], optional) - list of build instructions; each object has:
  - `context` (string) - build context directory
  - `location` (string) - path to the Dockerfile
  - `identifier` (string | null, optional) - optional label identifying the image
- `handout_dir` (string, optional) - directory with handout files.

## Flags

`flag` supports several shapes to accommodate static and advanced workflows.

- Single string flag:

  <details open>
  <summary>YAML</summary>

  ```yaml
  flag: flag{flag}
  ```

  </details>

  <details>
  <summary>JSON</summary>

  ```json
  "flag": "flag{flag}"
  ```

  </details>

- Array of string flags:

  <details open>
  <summary>YAML</summary>

  ```yaml
  flag:
    - flag{flag1}
    - flag{flag2}
  ```

  </details>

  <details>
  <summary>JSON</summary>

  ```json
  "flag": [
    "flag{flag1}",
    "flag{flag2}"
  ]
  ```

  </details>

- Array of objects (supporting case sensitivity and metadata):

  <details open>
  <summary>YAML</summary>

  ```yaml
  flag:
    - flag: flag{flag1}
      case_sensitive: true
    - flag: flag{flag2}
      case_sensitive: false
  ```

  </details>

  <details>
  <summary>JSON</summary>

  ```json
  "flag": [
    {
      "flag": "flag{flag1}",
      "case_sensitive": true
    },
    {
      "flag": "flag{flag2}",
      "case_sensitive": false
    }
  ]
  ```

  </details>

Each flag element may be `dynamic`, `null`, or a normal flag string. If `case_sensitive` is omitted, flag checking defaults to case-sensitive behavior.

### Flag format

The default flag format is `flag{...}`. However, the schema allows for 2-10 chars as the flag delimiters. For example, `FLAG[...]`, `CTF{...}`, `SECRET(...)`, etc.  
The delimiter must be a word char (`a-z`, `A-Z`, `0-9`, `_`).

If you want to use a set flag format for your CTF, you can fork the schema and modify the regex pattern for the `flag` field in the `schema.json` file. Look for the `flag` property and adjust the `pattern` attribute to match your desired format.

## Dockerfile locations

`dockerfile_locations` is an array of objects. Example:

<details open>
<summary>YAML</summary>

```yaml
dockerfile_locations:
  - context: demo/
    location: demo/Dockerfile
    identifier: web
```

</details>

<details>
<summary>JSON</summary>

```json
"dockerfile_locations": [
  {
    "context": "demo/",
    "location": "demo/Dockerfile",
    "identifier": "web"
  }
]
```

</details>

</br >

This tells automation: from `demo/` context, use `demo/Dockerfile` and tag the resulting image using `identifier` when provided.

The `identifier` field accepts either a string or `null` (i.e. `"type": ["string", "null"]` in the schema). Use `null`, an empty string, or the literal value `None` to indicate "no suffix" for the image tag. Example where no identifier/suffix is used:

<details open>
<summary>YAML</summary>

```yaml
dockerfile_locations:
  - context: demo/
    location: demo/Dockerfile
    identifier: null
```

</details>

<details>
<summary>JSON</summary>

```json
"dockerfile_locations": [
  {
    "context": "demo/",
    "location": "demo/Dockerfile",
    "identifier": null
  }
]
```

</details>

## Minimal example

<details open>
<summary>YAML</summary>

```yaml
enabled: true
name: "Demo Challenge"
slug: "demo-challenge"
author: "The Mikkel"
category: "misc"
difficulty: "easy"
type: "static"
instanced_type: "none"
flag: "flag{d3m0_fl4g}"
points: 1000
min_points: 100
description_location: "description.md"
```

</details>

<details>
<summary>JSON</summary>

```json
{
  "$schema": "https://raw.githubusercontent.com/ctfpilot/challenge-schema/refs/heads/main/schema.json",
  "enabled": true,
  "name": "Demo Challenge",
  "slug": "demo-challenge",
  "author": "The Mikkel",
  "category": "misc",
  "difficulty": "easy",
  "type": "static",
  "instanced_type": "none",
  "flag": "flag{d3m0_fl4g}",
  "points": 1000,
  "min_points": 100,
  "description_location": "description.md"
}
```

</details>

## Full example (advanced)

<details open>
<summary>YAML</summary>

```yaml
enabled: true
name: "Example Challenge"
slug: "example-challenge"
author: John Smith
category: web
difficulty: easy
tags:
  - web
  - easy
type: static
instanced_type: none
instanced_name: example-challenge
instanced_subdomains:
  - example
  - test
flag:
  - flag: flag{flag1}
    case_sensitive: true
  - flag: flag{flag2}
    case_sensitive: false
points: 1000
min_points: 100
description_location: description.md
prerequisites:
  - prerequisite-challenge
dockerfile_locations:
  - location: src/web/Dockerfile
    context: src/web/
    identifier: web
  - location: src/bot/Dockerfile
    context: src/bot/
    identifier: bot
handout_dir: handout
connection: http://example.com
```

</details>

<details>
<summary>JSON</summary>

```json
{
  "$schema": "https://raw.githubusercontent.com/ctfpilot/challenge-schema/refs/heads/main/schema.json",
  "enabled": true,
  "name": "Example Challenge",
  "slug": "example-challenge",
  "author": "John Smith",
  "category": "web",
  "difficulty": "easy",
  "tags": [
    "web",
    "easy"
  ],
  "type": "static",
  "instanced_type": "none",
  "instanced_name": "example-challenge",
  "instanced_subdomains": [
    "example",
    "test"
  ],
  "flag": [
    {
      "flag": "flag{flag1}",
      "case_sensitive": true
    },
    {
      "flag": "flag{flag2}",
      "case_sensitive": false
    }
  ],
  "points": 1000,
  "min_points": 100,
  "description_location": "description.md",
  "prerequisites": [
    "prerequisite-challenge"
  ],
  "dockerfile_locations": [
    {
      "location": "src/web/Dockerfile",
      "context": "src/web/",
      "identifier": "web"
    },
    {
      "location": "src/bot/Dockerfile",
      "context": "src/bot/",
      "identifier": "bot"
    }
  ],
  "handout_dir": "handout",
  "connection": "http://example.com"
}
```

</details>

## Linking to the schema

Add the following top-line to your YAML files to enable editor validation and autocompletion:

```yaml
# yaml-language-server: $schema="https://raw.githubusercontent.com/ctfpilot/challenge-schema/refs/heads/main/schema.json"
```

Add the following to your JSON files to enable editor validation and autocompletion:

```json
{
  "$schema": "https://raw.githubusercontent.com/ctfpilot/challenge-schema/refs/heads/main/schema.json"
}
```

*You may replace the URL with a local path to `schema.json` or to a specific version/release in the GitHub repository, such as with: `https://raw.githubusercontent.com/ctfpilot/challenge-schema/refs/tags/vX.X.X/schema.json`.*

## Notes and best practices

- Prefer `slug`-style names (lowercase, hyphen-separated) for identifiers and `instanced_subdomains`.
- Keep `points` and `min_points` within the defined ranges to avoid validation failures in automation.
- Use `dockerfile_locations` when challenge runtime requires custom images; include an `identifier` when multiple images exist.
- When in doubt about a field's format, open the canonical `schema.json` for the challenge folder.

## Contributing

We welcome contributions of all kinds, from **code** and **documentation** to **bug reports** and **feedback**!

Please check the [Contribution Guidelines (`CONTRIBUTING.md`)](/CONTRIBUTING.md) for detailed guidelines on how to contribute.

To maintain the ability to distribute contributions across all our licensing models, **all code contributions require signing a Contributor License Agreement (CLA)**.
You can review **[the CLA here](https://github.com/ctfpilot/cla)**. CLA signing happens automatically when you create your first pull request.  
To administrate the CLA signing process, we are using **[CLA assistant lite](https://github.com/marketplace/actions/cla-assistant-lite)**.

*A copy of the CLA document is also included in this repository as [`CLA.md`](CLA.md).*  
*Signatures are stored in the [`cla` repository](https://github.com/ctfpilot/cla).*

## License

This schema and repository is licensed under the **EUPL-1.2 License**.  
You can find the full license in the **[LICENSE](LICENSE)** file.

We encourage all modifications and contributions to be shared back with the community, for example through pull requests to this repository.  
We also encourage all derivative works to be publicly available under the **EUPL-1.2 License**.  
At all times must the license terms be followed.

For information regarding how to contribute, see the [contributing](#contributing) section above.

CTF Pilot is owned and maintained by **[The0Mikkel](https://github.com/The0mikkel)**.  
Required Notice: Copyright Mikkel Albrechtsen (<https://themikkel.dk>)

## Code of Conduct

We expect all contributors to adhere to our [Code of Conduct](/CODE_OF_CONDUCT.md) to ensure a welcoming and inclusive environment for all.
