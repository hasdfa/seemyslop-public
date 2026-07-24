---
name: share-with-seemyslop
description: Use when a user explicitly asks to showcase, publish, upload remotely, or return a public URL for a Markdown plan, document, static HTML preview, built website, or buildable web app.
---

# Share with SeeMySlop

## Overview

Publish requested files or static web output through `npx --yes seemyslop@0.1.0`. SeeMySlop links are public and unlisted unless eligible password protection is explicitly requested.

## Workflow

1. Confirm the request explicitly asks for remote sharing. A request to inspect, render, or preview locally is not upload authorization.
2. Resolve the source and inspect only filenames and necessary content for credentials, `.env*`, private keys, tokens, certificates, or unrelated private material. Stop before upload when risk is found; never print secret values.
3. Select the deployable input:
   - **Raw file:** Copy it byte-for-byte with its original filename into a temporary directory. Do not transform Markdown or documents.
   - **Static HTML:** Treat an existing standalone HTML preview as built. Stage it with required local assets and preserve filenames.
   - **Buildable app:** Read `packageManager`, lockfiles, build scripts, and framework config. Run the existing locked install only when dependencies are missing, then the existing build command. Deploy only the configured or uniquely identified static output (`dist`, `build`, or `out`). A normal Next.js `.next` directory is not static output.
   - **Built directory:** Deploy it directly.
4. Inspect the final deployment directory again. Exclude source, dependency trees, caches, source maps, environment files, and credentials. Do not upload source when a build fails.
5. Determine the artifact ID. Reuse an ID supplied by the user, parsed from a prior SeeMySlop URL, already known in the task, or uniquely matched by both repository and source path from `npx --yes seemyslop@0.1.0 artifacts --json`. Never guess from title alone. Omit `--id` when no reliable match exists.
6. Create no mapping state by default. If durable continuity is useful, an agent may store it in `~/.config/seemyslop/shares.json`; never add mapping files to the project.
7. Deploy with JSON output:

   ```bash
   npx --yes seemyslop@0.1.0 deploy "$DEPLOY_DIR" --json --title "$TITLE" --message "$MESSAGE"
   ```

   Add `--id "$ARTIFACT_ID"` only when the ID is reliable. For a staged raw file, add `--no-git-metadata`. If credentials are missing, run `npx --yes seemyslop@0.1.0 auth login`, hand the interactive token step to the user, and retry once.

8. Parse only a successful server response. Verify the current and immutable version URLs over HTTPS; for an app, also fetch its referenced JS/CSS. Do not claim a URL exists after failed deployment or verification.
9. Return `currentUrl`, `versionUrl`, and the URL-encoded direct file path when the entry is not `index.html`. State when a reused ID advanced the current version.
10. Remove temporary staging after the CLI finishes reading it.

## Constraints

- The CLI deploys directories, so raw files require temporary staging.
- Preserve raw bytes and original filenames.
- Never replace a failing app build with source upload.
- Do not silently choose a different ID after an ownership conflict.
- Static hosting has no server runtime. Verify SPA routes and external API requirements before calling an app functional.
