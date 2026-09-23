# Tektūra — Architecture Practice Website (Framer)

A premium website template for architecture practices, built in [Framer](https://framer.com). Tektūra is built around modular sections and CMS-driven content (projects, journal, team, awards, and more), so no two sites built from it need to share a layout.

> **Based on the Tektūra Framer template.** This project is built on **Tektūra**, a premium architecture-agency template with modular sections, CMS-driven projects and journal, and flexible layouts. Live demo: <https://tektura.framer.website/>. Designed by **Cristian Mielu** ([UI Hub Design](https://www.uihub.design/)). Please keep this attribution and refer to the template's license before redistributing.

## Tech & tooling

- **Platform:** Framer (visual website builder + design tool)
- **Editing via AI:** The project can be edited with AI two ways — Framer's built-in 3.0 Agent, which works directly inside the canvas, or an external agent (Claude Code, Cursor, Codex) connected through the Framer MCP server (`mcp.unframer.co`). Either can read and modify pages, components, styles, and CMS content. See [`AGENTS.md`](./AGENTS.md) for agent-facing conventions and how to supply that file to each.
- **Framer Skills:** [Skills](https://framer.link/xAHTjKz) are reusable, `/command`-invoked instruction sets (Settings → Skills) that teach Framer's built-in Agent your project's standards so it applies them consistently. A **design-system skill** paired with the [`/design-system`](#pages) page is the recommended setup here — it keeps AI-generated content on the template's existing components, styles, and tokens. See [`AGENTS.md`](./AGENTS.md#framer-skills).
- **Content model:** Heavily CMS-driven. Ten collections feed the projects, journal, team, services, awards, clients, and legal pages. Detail routes (`:slug`) are generated from those collections rather than hand-built per item.

## Site structure

### Pages

| Path | Purpose |
| --- | --- |
| `/` | Home / landing page |
| `/about` | About the practice |
| `/projects` | Projects index |
| `/projects/:slug` | Individual project detail (CMS) |
| `/articles` | Journal / articles index |
| `/articles/:slug` | Individual article detail (CMS) |
| `/contact` | Contact + open roles (`#open-roles`) |
| `/legal/:slug` | Legal pages — privacy, cookie policy, etc. (CMS) |
| `/design-system` | Published design-system reference page — the basis for a Framer design-system skill (see below) |
| `/404` | Not-found page |

Two design pages, **Design** and **Documentation**, are used for canvas exploration and are not published.

### CMS collections

All ten collections are user-managed:

| Collection | Notable fields | Feeds |
| --- | --- | --- |
| **Projects** | Client (ref), Categories, Services, Teams, Location, stats (SQM, CO₂, carbon avoided, energy), cover + 3 images, and section content (Brief, Site & context, Concept, Materiality, Sustainability, Outcome) | `/projects`, `/projects/:slug` |
| **Articles** | Date, Author (→ Team), Categories, Image, Excerpt, Content | `/articles`, `/articles/:slug` |
| **Team** | Role, Twitter, LinkedIn, Image, Content | Team section, article authors |
| **Services** | Categories, three images, Content | Services section |
| **Categories** | Title, Content | Referenced by Projects, Services, Articles |
| **Clients** | Logos, Representant (name/role/photo/quote) | Client testimonials |
| **Locations** | Title, Content | Referenced by Projects; footer locations |
| **Awards** | Work (→ Projects), Award, Year | Awards & recognition |
| **Legal** | Effective date, Intro, Content | `/legal/:slug` |
| **FAQ** | Title, Answer | FAQ section |

> Because Projects references Clients, Services, Team, Locations, and Categories, populate those collections **before** creating project items, or the references will have nothing to point at.

### Key components

The template is deeply componentized (75+ components). Major reusable pieces include:

- **Navigation & layout:** `Nav`, `Menu`, `Menu links`, `Logo`, `Footer`, `Locations`
- **Hero variants:** `Hero`, `Work Hero`, `Projects Hero`, `About Hero`, `Work Content`
- **Home sections:** `Positioning statement`, `Selected work`, `Statistics`, `Services`, `Process`, `Team Section`, `Clients & testimonials`, `Awards & recognition`, `Journal`, `FAQ`, `CTA`, `Newsletter`
- **Cards:** `Work Card`, `Service card`, `Experience card`, `Team member card`, `Client Testimonial Card`, `Article Card`, `Statistics Card`, `Role card`, `Client Logo Card`
- **Projects & articles:** `Selected work`, `Project Line`, `Articles`, `Article Content`, `Journal`, `Empty State`
- **Contact / recruitment:** `Contact content`, `Inquiry form`, `Open roles`, `Join team card`, `Email and phone`
- **Interaction:** `Testimonials Slider`, `Slider Handle`, `Rotating Image`, `TextReveal Card`, `Tab`, `FAQ Tab`
- **Template / misc:** `My Profile`, `Framer link`, `Numbers`, `Copyright and Year`, `404 Content`, `Legal Content`

> Note: there are two `Newsletter`-related components (`Newsletter` and `Newsletter form`) and several similar card/hero variants — see [`AGENTS.md`](./AGENTS.md) for how to disambiguate them.

## Design system

### Color styles

A refined grayscale system (no chromatic accent) — dark neutrals through light neutrals, plus a few low-opacity tints:

- **Darks:** `Dark 2` (`rgb(5,5,5)`), `Dark 4`, `Dark 6`, `Dark 20`, `Dark 22`, `Dark 40`
- **Lights / whites:** `White 100`, `Light 98`, `Light 96`, `Light 92`, `Light 90`, `Light 80`, `Light 62`, `Light 60`
- **Tints:** `Dark 2 - 8%`, `Dark 2 - 3%`, `Light 90 - 8%`

### Typography

- **Headings:** Manrope (`Heading 1`–`Heading 4`, plus small variants)
- **Body & UI:** Inter Display (`Body Light/Dark`, `Subheadline`, `Name`, `Button`, `Menu Link`, `Link` sizes)
- **Mono / accents:** Intel One Mono (`Span`, `Span Small`)

To change a text color you must apply or create a **text style** — color cannot be set directly on a text node in Framer.

## Working on this project

1. Edit in Framer directly (including with Framer's built-in Agent), or connect an external agent via the Framer MCP server.
2. Before making structural changes, read the current state (whole project, then the specific page/component/collection you're editing).
3. Prefer reusing existing components and CMS collections over creating new ones.
4. For new marketing sections, Framer's pre-built section components are the fastest starting point.

See [`AGENTS.md`](./AGENTS.md) for detailed, agent-oriented rules and gotchas.

## Credits & license

- **Template:** Tektūra — Premium Architecture Agency Framer Template
- **Designer:** Cristian Mielu — [UI Hub Design](https://www.uihub.design/)
- **Live demo:** <https://tektura.framer.website/>
- **Built with:** Framer

This project is derived from a commercial Framer template. Keep the attribution above, and consult the original template's license terms before redistributing or reselling.
