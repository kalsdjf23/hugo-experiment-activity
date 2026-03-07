# hugo-experiment-activity

`hugo-experiment-activity` is a standalone Hugo Module that renders a GitHub-style activity overview from a section of your Hugo content. It is designed for `experiments` by default and counts published pages by their `.Date`.

The module is self-contained:

- no dependency on the host site's CSS
- no JavaScript
- no direct filesystem coupling to the host repo
- works with both leaf bundles and flat Markdown files

## What It Renders

- a GitHub-style activity card
- a Monday-first week grid
- month labels across the top
- no weekday label column; the full width is used for the day cells
- a binary activity state: neutral for no post, green for published
- a clearer legend with larger swatches for `No post` and `Published`
- a start marker that shows when the first published experiment in the section was posted
- responsive sizing that scales to the width of its host container without horizontal scrolling

By default it renders the last `365` days and generates a title like `17 AI experiments in the last year`.
If you pass `title`, that custom text is shown instead of the auto-generated count-based heading.

## Module API

### Partial

```go-html-template
{{ partial "hugo-experiment-activity/activity.html" (dict "page" . "section" "experiments" "days" 365 "title" "") }}
```

### Shortcode

```go-html-template
{{< experiment-activity section="experiments" days="365" title="" >}}
```

### Options

- `page`: required for the partial; pass the current page context
- `section`: optional, defaults to `experiments`
- `days`: optional, defaults to `365`
- `title`: optional; when omitted, the module auto-generates headings like `17 AI experiments in the last year`; when set, it fully replaces that default heading

## Install In A Hugo Site

Official Hugo docs:

- [Hugo Modules introduction](https://gohugo.io/hugo-modules/introduction/)
- [Module configuration](https://gohugo.io/configuration/module/)

Hugo Modules require a working `go` binary on your `PATH`.

If your site does not already use Hugo Modules, initialize it once from the site root:

```bash
hugo mod init github.com/hugomelis/your-site
```

Then add the import to your site's `hugo.toml`:

```toml
[module]
  [[module.imports]]
    path = "github.com/hugomelis/hugo-experiment-activity"
```

If you want to fetch it immediately instead of waiting for the next Hugo build:

```bash
hugo mod get github.com/hugomelis/hugo-experiment-activity
```

## Use In The Host Site

Recommended partial call in a layout:

```go-html-template
{{ partial "hugo-experiment-activity/activity.html" (dict "page" . "section" "experiments") }}
```

Example shortcode use in Markdown:

```md
## Activity

{{< experiment-activity >}}
```

Example shortcode with a custom heading:

```md
{{< experiment-activity title="AI experiment activity in the last year" >}}
```

## How The Counting Works

The module:

- reads from `.Site.RegularPages`
- filters to the requested `section`
- excludes drafts
- excludes expired pages
- excludes pages outside the visible time window
- groups posts by `page.Date.Format "2006-01-02"`

Any day with one or more published pages uses the same active color. The module does not use density gradients.
Cell labels and hover text use experiment wording, for example `No experiments on Mar 4, 2026`, `1 experiment on Mar 4, 2026`, or `2 experiments on Mar 4, 2026`.
The legend also shows the first published experiment date in the filtered section, for example `Started on Feb 18, 2026`.

That means helper files inside page bundles such as `plan.md` or `prompt1.md` are not counted unless Hugo exposes them as regular pages.

## Integrating Into The Current Main Site

The current `hugomelis.nl` site at `/Users/hugo/OpenClaw/CODEX integration` is not a Hugo Module yet, so the other Codex thread should do this first in that repo:

```bash
hugo mod init github.com/hugomelis/hugomelis-site
```

Then add the module import to `hugo.toml`:

```toml
[module]
  [[module.imports]]
    path = "github.com/hugomelis/hugo-experiment-activity"
```

Then render it where you want it, for example in a layout:

```go-html-template
{{ partial "hugo-experiment-activity/activity.html" (dict "page" . "section" "experiments") }}
```

No content restructure is needed. Hugo already treats the real experiment posts in `content/experiments` as regular pages and ignores helper Markdown files inside bundles for page listing.

## Local Smoke Test

This repo includes `exampleSite/` wired to the local module via a `replace` directive.

Build it with:

```bash
hugo --source exampleSite --destination /tmp/hugo-experiment-activity-example --noBuildLock
```

If that command fails with a missing `go` binary, install Go or expose it on your `PATH` first, then rerun the build.

The example site includes:

- one flat-file experiment post
- one bundle-based experiment post
- two posts on the same day to verify counting text and the binary active state
- one draft experiment that should not count
- one future experiment that should not count
- one expired experiment that should not count
- an empty-state page using a missing section

## Repo Layout

```text
layouts/
  partials/hugo-experiment-activity/activity.html
  shortcodes/experiment-activity.html
exampleSite/
  go.mod
  hugo.toml
  content/
  layouts/
```
