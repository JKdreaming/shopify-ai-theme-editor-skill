---
name: shopify-ai-editor-setup
description: Guide a person through connecting an AI coding workspace to a Shopify theme, saving setup instructions in their chosen folder, previewing edits, and uploading reviewed changes to a draft.
---

# Connect an AI editor to Shopify

Guide the person through setup in the coding harness they already use. Support Claude Code, ChatGPT/Codex, Gemini CLI, DeepSeek through a compatible coding editor, and other assistants with equivalent tools. The workflow uses local files and Shopify CLI. A model name or subscription does not itself provide filesystem, shell, network, or Shopify access.

If the current interface only supports conversation, explain the steps and have the person run them in a local coding workspace. Do not claim a connection or perform fictional tool actions. Native skill discovery and installation vary by harness; this Markdown file can also be supplied explicitly as project instructions.

## 1. Establish the destination

Ask for the folder the person wants to use, their AI coding tool, and the store's permanent `myshopify.com` address. A folder explicitly selected in the current task can be used; otherwise ask for the path. Do not assume an unrelated open repository is the intended theme project.

Explain that the initial setup will create a human-readable `SHOPIFY-AI-SETUP.md` inside that chosen folder and download the selected theme into a dedicated `theme/` subfolder. Respect a different layout if requested. Resolve the path and inspect the folder before writing. Preserve existing files and instructions. If `SHOPIFY-AI-SETUP.md` already exists, read it and update it only when it belongs to this setup; otherwise choose a sibling filename.

Copy `references/SHOPIFY-AI-SETUP.template.md` into that file when the bundled template is available. Otherwise create an equivalent document with the destination, nonsecret store and draft identifiers, commands tailored to the operating system, completed steps, validation results, and next action. Replace the template's bracketed prompts with known information or “Not selected yet.” Never include credentials, session URLs, or private customer information.

Keep this document current as the person proceeds. Mark a step complete only after its result is observed. Put theme files in the selected theme subfolder, and run Shopify commands there. This prevents a later pull from deleting the setup instructions in the parent folder.

## 2. Confirm access and boundaries

The person needs permission to work on the store's themes and a source theme they may use. Identify the published theme and the draft target. Do not infer that the theme ID in an old message is still a draft.

Default to a separate draft, with a small local edit followed by review. Do not publish, modify the live theme, change store-wide data, install a Shopify app, create access credentials, or expand permissions without authorization for that action. Permission to connect an editor is not permission to release a storefront.

Theme duplication does not duplicate products, menus, pages, apps, or the store database. Limit this setup to theme files. Stop for authentication, CAPTCHA, security, or permission barriers that require the person's action; do not bypass them.

## 3. Check the local tools

Inspect the available runtime and installed CLI before installing anything. Check the official requirements if the installed version differs from the examples. Shopify CLI currently requires Node.js 22.12 or later and Git 2.28 or later.

```sh
node --version
git --version
shopify version
```

If Shopify CLI is missing and installation is authorized, install it using the official package:

```sh
npm install -g @shopify/cli@latest
shopify version
```

On Windows, use the person's chosen PowerShell or WSL environment consistently. The shell snippets below use Bash-compatible syntax. Translate the variable syntax for PowerShell; do not silently switch their environment.

## 4. Authenticate without exposing a secret

Prefer Shopify's interactive account login for a person working locally. Run a store command and let the person complete the authentication prompt. A Chrome login and a CLI login are separate sessions.

```sh
SHOPIFY_STORE="REPLACE_WITH_STORE.myshopify.com"
shopify theme list --store "$SHOPIFY_STORE"
```

Use a store owner or authorized staff/collaborator account with the appropriate Themes permission. Development stores have additional account rules; check the official guidance if access is rejected. Preserve any existing working authentication context unless the person asks to change it.

Do not ask the person to paste a credential into the AI conversation. Do not read browser cookies, session storage, or internal iframe/session URLs to obtain authentication.

## 5. Offer a dedicated theme password only if needed

Account login does not require a new Shopify API key. For automation or a separate developer credential, explain Shopify's Theme Access app as an optional route:

1. An authorized person installs the official Theme Access app if needed.
2. Open the app and choose **Create theme password**.
3. Enter the intended developer's contact details and create the password.
4. The recipient follows Shopify's email link and stores the one-time displayed value privately.
5. Configure it through a private secret store or protected process environment as `SHOPIFY_CLI_THEME_TOKEN`.

Guide the person through secret entry privately. Do not write the value to the setup document, Git, an MCP configuration, a visible command argument, a recording, or a chat. Do not assume a `.env` file is automatically loaded by Shopify CLI.

Record only a credential's nonsecret name or management ID and whether it was created for this task. Do not create a broad custom app as a shortcut. A Storefront API key is not theme-write authorization; direct theme-writing APIs can have additional Shopify requirements.

## 6. Select and duplicate the source theme

Have the person open **Online Store**, identify the source theme, and use its actions menu to choose **Duplicate** when a working copy is needed. Give the copy a clear name. Only perform the duplication yourself when the person's instructions authorize it.

Refresh the CLI theme list and match the new draft's name, ID, and status. Record the ID in `SHOPIFY-AI-SETUP.md`. Store and theme identifiers are not passwords, but keep private business information out of shared deliverables.

## 7. Pull into an empty theme folder

Create or use the agreed `theme/` subfolder. If it already contains files, inspect and preserve them first. Pulling can overwrite local files and remove files missing remotely; never pull over uncommitted work blindly.

```sh
mkdir theme
cd theme
DRAFT_THEME_ID="REPLACE_WITH_VERIFIED_DRAFT_ID"
shopify theme pull --store "$SHOPIFY_STORE" --theme "$DRAFT_THEME_ID"
```

Verify the resulting theme structure: folders can include `assets/`, `sections/`, `snippets/`, `templates/`, `layout/`, `locales/`, and `config/`. Theme structures vary; inspect rather than requiring every optional folder.

## 8. Save a baseline

Use the repository already present when appropriate. For a new standalone theme folder, initialize Git and exclude secret/environment files, CLI state, and dependency directories before staging. Preserve existing ignore rules. Review the proposed staged files for secrets without printing secret values.

```sh
git init
```

Suggested ignore entries:

```gitignore
.env
.env.*
.shopify/
node_modules/
```

After checking the file list, save the initial theme as a baseline commit if Git identity is configured. If commit creation is blocked, preserve a local backup and explain the exact condition. Do not claim a commit exists when it failed. Never push a repository or create a public remote as part of this setup unless requested.

## 9. Open the project in the chosen harness

Point the AI coding workspace at the theme folder. Confirm the active directory and that it can read files, edit files, and run Shopify CLI. Launch the chosen CLI there or select the folder through the app's normal project controls.

- Claude: use Claude Code with local project access.
- ChatGPT/Codex: use an authorized local coding workspace, Codex app, CLI, or equivalent supported surface.
- Gemini: use Gemini CLI or a coding integration with file and shell tools.
- DeepSeek: select the provider in a compatible coding harness; the harness supplies file and execution tools.

Provider login is separate from Shopify login. If the person needs an AI API key, guide them to the provider's official key-management page, choose the correct account/project, create a dedicated key, and enter it in the harness's private settings. Respect their chosen provider and billing constraints; do not obtain a different provider's key as a workaround.

If useful, add a short reference to `SHOPIFY-AI-SETUP.md` in the harness's existing instruction file. Preserve unrelated `AGENTS.md`, `CLAUDE.md`, or `GEMINI.md` content. Do not install all three formats or global skills without a reason. Explicitly loading this skill is enough when native discovery is unavailable.

## 10. Add Shopify developer context only when useful

Shopify AI Toolkit or the Dev MCP server can provide current developer context. They are optional and do not replace store authentication. Follow the current official setup for the selected client. For example, Shopify documents:

```sh
claude mcp add --transport stdio shopify-dev-mcp -- npx -y @shopify/dev-mcp@latest
```

For Codex CLI, the corresponding configuration is:

```toml
[mcp_servers.shopify-dev-mcp]
command = "npx"
args = ["-y", "@shopify/dev-mcp@latest"]
```

Merge configuration carefully, preserve existing servers, and restart the client when required. Do not copy one harness's configuration syntax into another. Only install an integration within the person's authorized scope.

## 11. Establish the initial check results

Run `shopify theme check` before editing and save a concise summary of existing findings. Avoid verbose logs that can contain sensitive data. This baseline helps distinguish existing defects from regressions.

Ask for a specific first change. An example is improving mobile hero heading wrapping while retaining wording, images, links, claims, and desktop styling. Inspect the theme and propose the affected files before editing. A narrow task should not replace the whole theme or change unrelated settings.

## 12. Edit locally and inspect the diff

Make only the requested local change. Run the appropriate code checks, then review `git diff --stat` and `git diff`. Confirm every changed file is relevant. Preserve the merchant's saved configuration and app integrations.

Update the setup document with changed paths and tests, without copying credentials or lengthy private output into it. If the AI has only conversation capabilities, provide a patch the person can review and apply rather than claiming to edit files directly.

## 13. Preview in Shopify

From the theme folder:

```sh
shopify theme dev --store "$SHOPIFY_STORE"
```

With no existing target supplied, this uses a temporary development theme. It uploads files for preview and uses store data; it is not an offline sandbox. Open the printed preview URL and keep the process running during inspection.

Test phone, tablet, and desktop layouts, keyboard behavior, navigation, images, and the main CTA. Test affected shopping controls such as variants, cart drawer, and add to cart where relevant. Do not place a real paid order without explicit authorization. Passing Theme Check does not prove the browser experience works.

## 14. Reconcile collaborators' changes

Before uploading, ask whether anyone has edited the same draft in Shopify's visual editor. Check the remote state and pause conflicting work. Preserve the local diff, pull the current remote theme into a separate clean directory if necessary, and compare before merging.

Treat `config/settings_data.json` and JSON templates carefully. `--theme-editor-sync` is an optional development feature with conflict behavior that must be understood before enabling it; it does not solve every collaboration conflict.

## 15. Upload only to the approved draft

Obtain the person's authorization for the destination and upload when not already provided. Recheck the target's current status. Never use live/publish flags in a draft-only setup.

Use the actual changed paths, not the example paths below:

```sh
shopify theme list --store "$SHOPIFY_STORE"
shopify theme push --store "$SHOPIFY_STORE" --theme "$DRAFT_THEME_ID" --only "assets/ACTUAL_CHANGED_FILE.css" --nodelete --strict
```

Repeat `--only` for each intended file. `--nodelete` prevents removal of remote files missing locally; it does not prevent selected files from being overwritten. `--strict` requires Theme Check to pass without errors. Resolve blocking errors rather than quietly removing checks.

If the person wants a new persistent draft from the complete reviewed theme instead:

```sh
shopify theme push --store "$SHOPIFY_STORE" --unpublished --theme "AI editor review draft" --strict
```

Save the new ID returned by Shopify. Do not confuse the name for a newly created draft with the existing ID used for later updates.

## 16. Verify and hand over

Open the actual hosted preview and editor links returned by Shopify. Verify the target name, draft status, changed behavior, and affected interactions. Report real results and unresolved issues. An upload receipt proves transfer, not visual or functional correctness.

Update `SHOPIFY-AI-SETUP.md` with the nonsecret draft ID, changed files, check results, next command, and an explicit statement that publication has or has not happened. Share a preview only with the intended audience. Do not publish as an automatic final step.

## 17. Clean up temporary access

For a task-specific Theme Access password, open Theme Access → Passwords, select Delete beside that credential, and confirm revocation with the person's authorization where required. For temporary AI API keys, revoke them at the issuing provider. Delete only credentials created for this task, never an existing key that might support other workflows.

Remove temporary secret copies and stop recording before any credential screen. Removing an environment variable does not revoke the credential. Retain only sanitized screenshots and logs. If a credential was exposed, revoke it even if the published video is later blurred.

For screen recording, capture only the relevant window/region when supported. Pause before secret entry or reveal; exclude account menus, unrelated tabs, customer data, and internal session URLs. If sensitive information appears, fully mask the affected region for every visible frame and optionally add blur. Inspect the exported frames before sharing. Never claim footage is sanitized without checking it.

End with a concise handoff: chosen folder, setup document path, store/draft selection, validation evidence, remaining access, and the next action. If a prerequisite failed, leave the correct pending step in the document and explain what the person must do.

## Official references

Consult only the relevant current pages when a command, permission, or client configuration differs:

- Shopify CLI: https://shopify.dev/docs/api/shopify-cli
- Authentication and development themes: https://shopify.dev/docs/storefronts/themes/tools/cli
- Theme Access creation/revocation: https://shopify.dev/docs/storefronts/themes/tools/theme-access
- Pull: https://shopify.dev/docs/api/shopify-cli/theme/theme-pull
- Preview: https://shopify.dev/docs/api/shopify-cli/theme/theme-dev
- Upload: https://shopify.dev/docs/api/shopify-cli/theme/theme-push
- AI Toolkit: https://shopify.dev/docs/api/ai-toolkit
- Codex CLI: https://learn.chatgpt.com/docs/codex/cli
- Claude Code: https://code.claude.com/docs/en/overview
- Gemini CLI: https://geminicli.com/docs/
- DeepSeek integrations: https://api-docs.deepseek.com/
