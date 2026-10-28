<p align="center"><img src="https://www.appstorepack.com/logo-512.png" width="96" alt="AppStorePack logo"></p>

# AppStorePack MCP server

**Create App Store screenshots, app icons and store copy for your iOS app — from Claude or any MCP client — then export the bundle or push it straight to App Store Connect.**

AppStorePack is a hosted (remote) MCP server. There's nothing to install or run: connect your client to the URL below and sign in.

```
https://www.appstorepack.com/api/mcp
```

- **Transport:** Streamable HTTP
- **Auth:** OAuth (you sign in to AppStorePack when you connect), or a personal access token (`Authorization: Bearer asp_…`) from your AppStorePack Account page
- **Website:** https://www.appstorepack.com

## What you can do

Ask your assistant things like:

- *"Make App Store screenshots for my meditation app. Pick a calm template and write captions from my description."*
- *"Here are my screenshots and logo — put them into the design and make the icon."*
- *"Add a panel for the new widgets feature, styled like the rest."*
- *"On the iPhone Duo screenshots, make the second caption bigger and move the phone down a little."*
- *"Write my App Store title, subtitle, keywords and description within Apple's limits."*
- *"Push the listing to App Store Connect."* (always shows you a plan of what will change first, and never submits for review)

Under the hood:

- **48 designed screenshot templates** for iPhone 6.9", iPad 13" and the iPhone Duo displays, rendered at Apple's exact sizes.
- **Per-device fine-tuning:** adjust a panel's device placement, caption and background on one device or all of them; every change is checked (caption inside the panel and clear of the device, readable on its background) before it's saved.
- **Captions and store copy validated against Apple's rules** (character limits, keyword formatting) before they're saved.
- **Uploads that bypass the chat:** in Claude Desktop / claude.ai, a drop zone appears in the conversation for screenshots and your logo; agents with a shell get one-time `curl` upload URLs.
- **App icon from your logo:** composed into the opaque 1024×1024 master Apple requires.
- **Export** a ready-to-upload zip, or **push** name, subtitle, keywords, promotional text, description and screenshots to App Store Connect (with your own App Store Connect API key).

## Connect

**Claude Desktop / claude.ai** — Settings → Connectors → *Add custom connector* → paste `https://www.appstorepack.com/api/mcp`, then sign in.

**Claude Code**

```bash
claude mcp add --transport http appstorepack https://www.appstorepack.com/api/mcp
```

**Other clients (Cursor, VS Code, Windsurf, …)** — add a remote/HTTP MCP server with the URL above. Clients that support MCP OAuth will prompt you to sign in; otherwise create a token on your AppStorePack Account page and send it as `Authorization: Bearer asp_…`.

```json
{
  "mcpServers": {
    "appstorepack": { "url": "https://www.appstorepack.com/api/mcp" }
  }
}
```

## Pricing

Designing screenshots, icons and copy is free. Exporting the full-resolution bundle and pushing to App Store Connect need an AppStorePack subscription ($19/month or $69/year).

## Tools

31 tools. Read-only tools are marked; the only tool that changes App Store Connect (`asc_push`) requires an explicit confirmation after a dry-run plan.

### Projects

| Tool | What it does |
|---|---|
| `list_projects` | List the user's AppStorePack projects (one per app), most recently updated first. *(read-only)* |
| `create_project` | Create a new AppStorePack project for an app (one project per app). |
| `update_project` | Change a project's name, category, pitch, iPad support, iPhone Duo support or locale. Turning on iPad or iPhone Duo adds that device's screenshot sets. |
| `get_project` | Full details for one project: app name, category, pitch, locale, whether it supports iPad / iPhone Duo and has an icon, the App Store copy fields (title, subtitle, keywords, promoText, description) with any rule violations, and the screenshot design (template, devices with their panel counts, and whether it's ready to export). *(read-only)* |

### Screenshot design

| Tool | What it does |
|---|---|
| `browse_templates` | See all App Store screenshot templates: one image with every template's first panel (numbered, with sample app screens), plus a list with each template's id, name, category, mood, panel count and placeholder captions. *(read-only)* |
| `preview_template` | Every panel of one template side by side (with sample app screens), for iPhone, iPad or an iPhone Duo display. *(read-only)* |
| `apply_template` | Switch a project's screenshot design to a template. |
| `get_design` | The project's current screenshot design: template, each panel's look (background, device composition, caption placement), every caption slot (slot number, role, where it appears, current headline and subtitle, whether a subtitle line exists), caption limits, and for each device which panels have the user's screenshot, whether its layout follows the iPhone design or is its own, and each panel's current layout values (device position, size, tilt, crop and frame; caption anchor, offset and size; background). *(read-only)* |
| `add_panel` | Add a panel to the project's design, styled like the template: it copies one of the design's own panel looks (background, device placement, decorative shapes, caption style) and gets an empty screenshot slot. |
| `remove_panel` | Remove one panel (and its screenshot) from the project's design, on every device. |
| `set_captions` | Set the headline (and subtitle where the template has one) for caption slots from get_design. |
| `adjust_panel` | Fine-tune one panel's layout — the device's position, size, tilt, crop and frame, the caption's anchor, offset and size, and the background colour — on one device (iPhone 6.9", iPad 13", iPhone Duo outer or inner) or all of them. Refuses with reasons instead of saving a broken layout; returns a preview. |
| `preview_design` | All panels of the project's current design for one device, side by side, with the user's screenshots and captions — to check the set reads well as a sequence before exporting or pushing. *(read-only)* |
| `preview_screenshot` | Render one App Store screenshot panel of the project's current design and return it as an image (downscaled to at most 900px). *(read-only)* |

### Creative assets (product page header & search results)

| Tool | What it does |
|---|---|
| `preview_creative_assets` | Render the project's App Store creative assets — the landscape product page header and search results images shown on iOS 27+ — as a 16:9 Universal asset plus dedicated 21:9 header and 3:2 search versions, with Apple's safe area marked. *(read-only)* |
| `set_creative_assets` | Set the headline (caption) and layout of the creative assets — subtitle, icon, devices, tilt, which panel's look — checked against Apple's content rules; the first call adds them to the export bundle. |

### Screenshots & icon upload

| Tool | What it does |
|---|---|
| `request_screenshots` | Show the user an upload panel in the chat where they drop their app screenshots onto each panel of the project's design (iPhone and, if enabled, iPad and the iPhone Duo displays), and their logo for the app icon. *(read-only)* |
| `get_screenshot_upload_url` | For agents that can run commands (e.g. Claude Code): a one-time URL (valid 15 minutes) to upload the user's app screenshot for one panel of one device. |
| `refresh_screenshot_previews` | Re-render the project's saved screenshot previews (the Export step thumbnails) after uploading screenshots. |
| `request_icon` | Show the user a drop zone in the chat for their app's logo, which becomes the App Store icon (see get_icon_upload_url for how it's composed). *(read-only)* |
| `get_icon_upload_url` | For agents that can run commands (e.g. |
| `set_icon_style` | Re-compose the app icon from the uploaded logo with a different background and/or logo size, and return a preview. |
| `preview_icon` | The project's App Store icon as it looks on a home screen (rounded mask applied), plus its background and logo size. *(read-only)* |

### Store copy

| Tool | What it does |
|---|---|
| `get_store_copy_rules` | Apple's rules for each App Store copy field: character limit, whether the field is indexed for search, formatting rules and writing guidance. *(read-only)* |
| `set_store_copy` | Save App Store copy fields for a project. |

### Export & App Store Connect

| Tool | What it does |
|---|---|
| `export_bundle` | Build the App Store Connect bundle for a project: a zip with full-resolution screenshots for each device (rendered on the server from the current design, in the user's chosen order), the 1024px icon if present, the creative assets if set up, the store copy as JSON, and a manifest. |
| `asc_status` | Whether the user has connected App Store Connect (an API key saved on their Account page), and which key. *(read-only)* |
| `asc_list_apps` | List the apps in the user's App Store Connect account (id, name, bundle id), to link a project with asc_link_project. *(read-only)* |
| `asc_link_project` | Record which App Store Connect app a project publishes to. |
| `asc_plan_push` | Show exactly what asc_push would change in App Store Connect for a project: the target app and editable version, each text field's current vs new value, how many screenshots per device would be replaced (with App Store Connect's placement group and its maximum count), and any blockers. *(read-only)* |
| `asc_push` | Fill the project's App Store Connect listing: updates name, subtitle, keywords, promotional text and description for the project's language (skipping fields the project leaves empty), and REPLACES the screenshots for each of the project's devices — iPhone 6.9", plus iPad 13" and the iPhone Duo outer/inner displays when the project has them on — with the project's current design (rendered on the server, in the user's chosen order). *(changes App Store Connect — asks for confirmation)* |

## Privacy & security

Your App Store Connect key is encrypted at rest and only used for requests you make. See https://www.appstorepack.com/security and https://www.appstorepack.com/privacy.

## Support

support@appstorepack.com

---

This repository holds the public listing for the AppStorePack MCP server (description and registry metadata). The service itself is closed source. AppStorePack is not affiliated with Apple; App Store is a trademark of Apple Inc.
