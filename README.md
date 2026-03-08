# hugo-experiment-activity

`hugo-experiment-activity` is now a compatibility wrapper around the generic open-source module [`hugo-activity-heatmap`](https://github.com/kalsdjf23/hugo-activity-heatmap).

Use this repo if you want to keep the original experiment-specific API:

- partial path: `hugo-experiment-activity/activity.html`
- shortcode: `{{< experiment-activity >}}`
- default section: `experiments`
- default wording: `AI experiment` / `AI experiments`
- default mode: binary
- start marker enabled by default

For new projects, prefer the generic module:

- [`github.com/kalsdjf23/hugo-activity-heatmap`](https://github.com/kalsdjf23/hugo-activity-heatmap)

## Install

Hugo Modules require a working `go` binary on your `PATH`.

If your site does not already use Hugo Modules, initialize it once from the site root:

```bash
hugo mod init github.com/your-user/your-site
```

Then add the wrapper import to your site's `hugo.toml`:

```toml
[module]
  [[module.imports]]
    path = "github.com/kalsdjf23/hugo-experiment-activity"
```

Optionally fetch it immediately:

```bash
hugo mod get github.com/kalsdjf23/hugo-experiment-activity
```

## Use It

Partial:

```go-html-template
{{ partial "hugo-experiment-activity/activity.html" (dict "page" . "section" "experiments") }}
```

Shortcode:

```md
{{< experiment-activity >}}
```

Custom title:

```md
{{< experiment-activity title="AI experiment activity in the last year" >}}
```

## Wrapper Behavior

This wrapper delegates to the generic heatmap module with fixed experiment defaults:

- reads from `.Site.RegularPages`
- filters to the `experiments` section unless you override `section`
- uses `.Date`
- excludes drafts, expired pages, and future-dated pages
- renders a binary active/inactive heatmap
- auto-generates headings like `17 AI experiments in the last year`
- uses experiment wording in cell labels, for example `1 AI experiment on Mar 4, 2026`
- shows a `Started on ...` legend item for the earliest published experiment

The clearer binary legend remains `No post` and `Published`.

## Generic Module

If you want a reusable component for blogs, notes, changelogs, or all regular pages, use:

- [`github.com/kalsdjf23/hugo-activity-heatmap`](https://github.com/kalsdjf23/hugo-activity-heatmap)

That module adds:

- `density` and `binary` modes
- configurable `section`
- configurable `date_field`
- configurable nouns
- optional start-date legend

## Local Smoke Test

This repo includes `exampleSite/` for verifying the wrapper behavior.

Build it with:

```bash
hugo --source exampleSite --destination /tmp/hugo-experiment-activity-example --noBuildLock
```
