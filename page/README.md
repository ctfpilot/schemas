# Page Schema

This schema defines the structure for describing static pages used by the platform (for example docs, information pages, or static site pages).  
It is a human-friendly reference of the JSON Schema in `schema/page/schema.json` and includes examples for YAML and JSON files.

## Quick reference — required fields

At minimum a page object SHOULD include the following properties (the schema lists them as required):

- `enabled` (boolean) — whether the page is enabled. Default: `true`.
- `slug` (string) — machine-friendly slug. Must match `^[a-z0-9-]+$`.
- `title` (string) — human-friendly title of the page.
- `route` (string) — route used for navigation (e.g. `/example-page`).
- `content` (string) — path to the content file for the page (must end with `.md`, `.html`, or `.txt`). Default: `page.md`.
- `format` (string) — either `markdown` or `html` (controls rendering). Default: `markdown`.

## Field constraints and allowed values

This section lists the main validation constraints defined in the schema.

- `version` (string, optional) — schema version (e.g. `1.0.0` or `1`). Fully optional; if omitted the latest schema version is assumed.
- `enabled`: boolean. Default `true`.
- `slug`: string matching regex `^[a-z0-9-]+$`. Min length 1, max length 50.
- `title`: string. Min length 1, max length 100.
- `route`: string. Min length 1, max length 100. Typically starts with a slash, e.g. `/demo`.
- `content`: string matching the pattern `^([a-zA-Z0-9-_.]+\.(md|html|txt))$`. This must point to a file in the repository such as `page.md` or `README.md`. Default `page.md`. Min length 1, max length 255.
- `auth`: boolean. Default `false`. When `true` the page requires authentication to view.
- `draft`: boolean. Default `false`. When `true` the page is a draft and not ready for public viewing.
- `format`: enum with values: `markdown`, `html`. Default `markdown`.

## Field reference (detailed)

- `enabled` (boolean)
  - Description: Whether the page is published/active. Use `false` for unpublished pages.

- `slug` (string)
  - Description: Lowercase slug used to identify the page. Allowed pattern: `^[a-z0-9-]+$`.

- `title` (string)
  - Description: The readable title displayed to users.

- `route` (string)
  - Description: Web route for navigation. Example: `/example-page`.

- `content` (string)
  - Description: Path to the content file relative to the page directory. Must end with `.md`, `.html`, or `.txt`.
  - Default: `page.md`.

- `auth` (boolean)
  - Description: When true, only authenticated users can access the page.
  - Default: `false`.

- `draft` (boolean)
  - Description: Marks the page as a draft. Draft pages are typically not displayed publicly.
  - Default: `false`.

- `format` (string)
  - Allowed values: `markdown`, `html`.
  - Default: `markdown`.

## Examples

YAML example (minimal):

```yaml
# yaml-language-server: $schema=./schema.json
enabled: true
slug: "demo-page"
title: "Demo Page"
route: "/demo-page"
content: "page.md"
format: "markdown"
```

JSON example (minimal):

```json
{
  "$schema": "./schema.json",
  "enabled": true,
  "slug": "demo-page",
  "title": "Demo Page",
  "route": "/demo-page",
  "content": "page.md",
  "format": "markdown"
}
```

## Linking to the schema

Add the following top-line to your YAML files to enable editor validation and autocompletion:

```yaml
# yaml-language-server: $schema="https://raw.githubusercontent.com/ctfpilot/schemas/refs/heads/main/page/schema.json"
```

Add the following to your JSON files to enable editor validation and autocompletion:

```json
{
  "$schema": "https://raw.githubusercontent.com/ctfpilot/schemas/refs/heads/main/page/schema.json"
}
```

*You may replace the URL with a local path to `schema.json` or to a specific version/release in the GitHub repository, such as with: `https://raw.githubusercontent.com/ctfpilot/schemas/refs/tags/vX.X.X/page/schema.json`.*

## Best practices and notes

- Use `slug` values that are short, lowercase, and hyphen-separated.
- Prefer `content` files in Markdown (`.md`) for ease of editing and portability unless you need raw HTML.
- Use `auth: true` sparingly — prefer to gate only genuinely restricted pages.
- `draft: true` is useful while authoring content to prevent accidental publication.
- When referencing the schema in editors, use the top-line comment for YAML shown above or the `$schema` property for JSON files to enable editor validation and autocompletion.
