# AGENTS.md

Instructions for AI agents operating on this Framer project. This file is for agents; [`README.md`](./README.md) is for humans. Framer 3.0's Agent ecosystem treats `AGENTS.md` as the place to encode project-specific rules, so keep this file current as the project evolves.

## How this file is actually used

Both Framer's own Agent and connected external agents can read and edit this project — pages, components, styles, and CMS content alike. This file applies to either; how you supply it differs:

- **Framer's built-in 3.0 Agent** (inside the canvas): edits content and layout directly. Give it this file's contents as context at the start of a session — paste it into the chat, or into your AI/project instruction settings if that surface exists — so it follows these conventions. Framer does not auto-load `AGENTS.md` from a repo the way a code editor does, so hand it over rather than assuming it's picked up.
- **External agents via MCP** (Claude Code, Cursor, Codex connected through Framer's MCP server): keep this file in the working directory and the agent reads it automatically.

The rules below apply to whichever agent is doing the work.

## Framer Skills

Framer's [Skills](https://www.framer.com/skills) are reusable instruction sets that teach the built-in Agent how to handle a recurring task in this project — invoked by a `/command` in agent chat (e.g. `/design-system`). They live in **Settings → Skills** (name, description, and a Content field of instructions). A skill differs from a one-off prompt in that it persists, so the Agent applies the same standards every time instead of you re-explaining them.

For this template, the highest-value skill is a **design-system skill** that mirrors the published [`/design-system`](https://tektura.framer.website/design-system) page: it should tell the Agent to build and edit content using this project's existing components, text styles (`/Heading 1`–`/Heading 4`, `/Body`, etc.), color tokens (`/Dark 2`, `/Light 96`, …), spacing, and breakpoints — not to invent new ones. This is the Framer-native equivalent of the "reuse, don't recreate" golden rule above, and the conventions in this `AGENTS.md` are good raw material to paste into that skill's Content field.

External MCP agents don't invoke Framer Skills; for them, this `AGENTS.md` plays the same role.

## What this project is

A website built on **Tektūra**, a premium architecture-agency Framer template by **Cristian Mielu** / [UI Hub Design](https://www.uihub.design/) — live demo at <https://tektura.framer.website/>. It is heavily CMS-driven (10 collections) with modular, swappable sections. See `README.md` for the full inventory.

**Attribution:** This is a derived commercial template. Do NOT remove the "Made in Framer" badge or the designer credit in the footer unless the user explicitly owns the appropriate license and asks. If asked to strip attribution, confirm the user has the right to do so first.

## Golden rules

1. **Read before you write.** An agent can't safely change what it hasn't looked at, and the write tools target things by ID, so always read first:
   - `getProjectXml` at the **start of a session** — the map of the whole project (every page, component, style, and collection, with their IDs). This only *lists* things; it doesn't show what's inside them.
   - `getNodeXml` on a **page or component before editing it** — this reveals the actual layers, text, and structure inside, so you edit the right node instead of working blind.
   - `getCMSCollections` **before touching content** — CMS items are written by opaque field IDs (e.g. `tEzFNbZCB`), not names; this call gives you those IDs.

   **Component instances vs. definitions.** A component (e.g. Footer) is defined once, then placed on many pages as *instances*. In a page's XML, an instance shows up only as a **reference** — "Footer sits here, `componentId` `snb6oNUyy`" — **not** its inner layers. To see or edit what's inside, take that `componentId` and call `getNodeXml` **on the component itself**. Editing the component updates every page that uses it; the instance reference on the page does not let you reach its internals.
2. **Reuse, don't recreate.** This template already has components, styles, and CMS collections for almost everything. Search the existing lists before building anything new.
3. **Edit incrementally.** Make small `updateXmlForNode` calls with only the nodes/attributes you're changing. Don't paste the whole XML tree back. Multiple small calls let the user watch changes land in real time.
4. **Never invent nodeIds or field IDs.** Use only ids returned by the read tools. New nodes are created by *omitting* `nodeId`; the tool returns the real ids to use next.
5. **Don't break responsiveness.** Insert page-level sections under the root **Desktop** breakpoint node, not inside other components, so breakpoints are preserved.

## CMS conventions

The content model is the backbone of this template. Ten collections:

`Categories`, `Services`, `Clients`, `Locations`, `Team`, `Projects`, `Articles`, `Awards`, `Legal`, `FAQ`.

- **Reference order matters.** `Projects` references `Clients`, `Services`, `Team`, `Locations`, and `Categories`; `Articles` references `Team` (Author) and `Categories`; `Awards` references `Projects`. Create/populate the referenced collections **first**, then the items that point at them, using the referenced items' **item IDs** (not slugs).
- **Field IDs are opaque strings** (e.g. `tEzFNbZCB`), not names. Get them from `getCMSCollections` and pass them exactly in `upsertCMSItem`.
- **`Projects` is large and section-structured** (dividers: Header, Data, Stats, Images, Content). Its rich-text fields — `Brief`, `Site & context`, `Concept`, `Materiality & construction`, `Sustainability`, `Outcome` — are `formattedText` (Markdown). Numeric stats (`SQM Area`, `CO2 value`, `Carbon avoided value`, `Energy in use value`) are numbers, not strings.
- **`Clients` has an `unsupported` field** (`Client Logos`) — it can't be written through the MCP tools. Leave it to the visual editor; don't try to set it.
- For image/file fields, pass a URL string as the value.

## Editing conventions

### Components
- To insert a section, prefer Framer's pre-built section components via `updateXmlForNode` with `insertUrl`.
- **Linked** instance (default): stays connected to the source component, so it updates when the source changes. You can edit its exposed props and instance-level styling (opacity, position, size, and any properties the component exposes), but you can't restructure its internal layers — to change those, edit the source component (via its `componentId`) so all instances update, or detach.
- **Detached** (`?detached=true` on the insertUrl): becomes plain, fully editable Frames with the component's internals exposed; does NOT track the source, so later changes to the source won't reach it. After detaching, re-run `getNodeXml` on the parent to see the generated internal structure before editing it.
- For code files, read `mcp://mcp.unframer.co/prompts/how-to-write-framer-code-files.md` first. (There are currently no code components or overrides in this project — adding one is a deliberate step.)

### Text & color
- A text node uses **either** `font` **or** `inlineTextStyle`, never both.
- **When editing via XML, text color is set through a text style, not a direct attribute.** A Text node has no color attribute in the XML — color rides on its text style, so apply an existing style or create a new one to change it. (In Framer's visual editor you can set a color on selected text directly; this constraint is specific to the MCP/XML workflow.)
- Reference design tokens by path, e.g. `backgroundColor="/Dark 2"`, `inlineTextStyle="/Heading 2"`.

### Layout
- Root-level / new screen nodes are absolute-positioned — use absolute positioning for them.
- Frames use `layout="stack"` (flex) or `layout="grid"`; respect existing layout choices when editing children.

## Project-specific gotchas

- **Duplicate / near-duplicate component names.** There are two `Newsletter`-related components (`Newsletter` = `mL1MqSIZ6`, `Newsletter form` = `BcV1sd2zw`), and multiple hero and card variants (`Hero` / `Work Hero` / `Projects Hero` / `About Hero`; several `*Card` components). **Always disambiguate by `nodeId`, never by name.** Confirm with the user if it's ambiguous.
- **`getProjectXml` is the source of truth** for the nodeId map below. IDs stay valid for this project copy, but if components are deleted and recreated they change — re-read rather than trusting a stale table.
- **Placeholder content is the template's, not the user's.** The live demo uses a fictional Dutch/Belgian practice ("Tektūra", founder Émile Laurent), `@tektura.com` emails, dummy phone numbers, and the designer's own social links (`x.com/CristianMielu`, etc.) on team cards. These are cleanup targets when adapting the template — replace them with the real brand's details.
- **Footer credit has a typo** on the live demo ("Cristiab Mielu"). If cleaning up, the correct name is Cristian Mielu.
- **CMS `:slug` routes.** `/projects/:slug`, `/articles/:slug`, `/legal/:slug` render from collections. Editing the page edits the *template* for every item — be explicit with the user about this blast radius.

## Known component → nodeId map

| Component | nodeId |
| --- | --- |
| Nav | `BLn3NrSGL` |
| Menu | `Nk5ZWXWTb` |
| Logo | `sUpLqgUul` |
| Footer | `snb6oNUyy` |
| Hero | `NVJJ6vsYY` |
| Projects Hero | `HYy6aiWm2` |
| About Hero | `TOtIbdZHn` |
| Work Hero | `Ef__wLuls` |
| Selected work | `SY0tayZbd` |
| Services | `kWmyhXm_3` |
| Process | `dAJIQ41Cr` |
| Team Section | `zARv1Ju5A` |
| Clients & testimonials | `lvsaWumt3` |
| Awards & recognition | `WcBww7irz` |
| Journal | `dsahIYjQw` |
| Statistics | `Y65OKQgXO` |
| FAQ | `HsdoufW8o` |
| CTA | `Kxy_gBe0_` |
| Newsletter | `mL1MqSIZ6` |
| Newsletter form | `BcV1sd2zw` |
| Inquiry form | `WIoR0v1Xh` |
| Open roles | `R5O9Pwxzk` |

(Run `getProjectXml` for the complete, authoritative list — this table is a convenience, not the source of truth.)

## Workflow checklist for any change

1. `getProjectXml` → confirm structure and focused node.
2. `getCMSCollections` if the change touches content.
3. `getNodeXml` on the target page/component (and on a component instance's `componentId`).
4. Make the smallest scoped `updateXmlForNode` / `upsertCMSItem` change.
5. Re-read affected nodes to confirm, then iterate.
