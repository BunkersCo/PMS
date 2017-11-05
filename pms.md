## Polymer Magic Server

The _Magic Server_ serves files directly from github (via `cdn.rawgit.com`) in a manner that is compatible with HTML Imports natural deduplication feature.

Examples:

**Load polymer from _master_ branch (full debug mode)**

```html
<base href="http://polygit.org/polymer+:master/components/">

<link href="polymer/polymer.html" rel="import">
```

**Load polymer v1.0.3, load latest release of _vellum-*_ from _sjmiles_ org**

```html
<base href="http://polygit.org/polymer+v1.0.3/vellum-*+sjmiles+*/components/">

<link href="polymer/polymer.html" rel="import">
<link href="vellum-grid/vellum-grid.html" rel="import">
```

### Features:
* stateless, stores nothing
* maintenance free (modulo GitHub API access limits)
* only serves files from `cdn.rawgit.com`

### Constraints:
* configuration must be embedded in all request URLs
* does no dependency resolution, user configuration is absolute
* only serves files from `cdn.rawgit.com`

This implementation uses a _fat URL_ scheme, where the user configuration is contained entirely in each URL (making the URLs potentially quite long). Other implementations may be possible using cookies or other schemes to eliminate the need for fat URLs.

This server concept is primarily for hit-and-run testing, jsbins, etc. The performance is less than ideal and there is very little error reporting or other niceties.

### Basic Usage

`<server-name>/[<configurations>/]components/<component>/<path>`

Currently a server is stood up on app-engine at `polymer-magic-server.appspot.com`, which is aliased as `polygit.org`. `components` is literal. `<component>/<path>` is the canonical path to a component resource.

Example using defaults (no `<configurations>`):

```html
<link href="http://polygit.org/components/polymer/polymer.html" rel="import">
```
Because the URL quickly becomes unwieldy, using `<base>` tag is recommended.

```html
<base href="http://polygit.org/components/">

<link href="polymer/polymer.html" rel="import">
```

http://jsbin.com/wihame/edit?html,output

### Default Configuration

Default configuration is to use serve the latest **release** version for each component. An organization is automatically selected for `polymer`, `webcomponents*`, `google-*` components. All other components are assumed to be `PolymerElements`.

### Custom Configurations

`<configurations>` are a series of configuration directives `<config1>[/<config2>...]`. 

Each `<config>` looks like `<component>[+<org>]+<ver>|:<branch>|*`.

`:<branch>` means pull from latest SHA on `<branch>`.
`*` means pull latest release.

Examples, 

Pull `polymer` from default org, `master` branch: `polymer+:master`
Pull `super-gif` from `sjmiles` org, latest release:  `super-gif+sjmiles+*` 
Pull `paper-button` from default org, `v1.0.2` release: `paper-button+v1.0.2`

Note: if `<org>` is specified, do not forget to also include version information (`+<ver>|:<branch>|*`).

Wilcards are supported in `component`

Pull all `super-` elements from `sjmiles` org: `super*+sjmiles`

### Example base URL with multiple configurations

`http://polygit.org/polymer+:master/super*+sjmiles+*/paper-button+v1.0.2/components/`

Remember that all URLs hitting the magic server must contain the complete configuration.
