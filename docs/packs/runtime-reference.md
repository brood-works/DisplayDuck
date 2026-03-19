---
icon: lucide/code-xml
---

# Pack runtime reference

This page covers what is available inside pack templates, styles, and runtime code.

## Template syntax

DisplayDuck templates support a small Handlebars-like syntax:

- `{{ expression }}`
- `{{#if expression}} ... {{/if}}`
- `{{#each expression}} ... {{/each}}`

Examples:

```html
<h1>{{ title() }}</h1>

{{#if showConfig()}}
  <div>Config is visible</div>
{{/if}}

{{#each Object.entries(config().config || {})}}
  <div>{{ this[0] }}: {{ this[1] }}</div>
{{/each}}
```

## What expressions can access

Template expressions run against the widget instance scope.

That means you can use:

- public methods on your `DisplayDuckWidget` class
- public signal properties on your class
- `payload`

Example:

```ts
export class DisplayDuckWidget {
  public title = signal('Hello');

  public buttonLabel(): string {
    return 'Click me';
  }
}
```

```html
<h1>{{ title() }}</h1>
<button>{{ buttonLabel() }}</button>
```

## Built-in placeholders

### `{{ASSETS}}`

`{{ASSETS}}` currently resolves to the pack directory base URL at runtime.

Examples:

```html
<img src="{{ASSETS}}/src/img/logo.png" alt="Logo" />
<a href="{{ASSETS}}/data/example.json">Open data</a>
```

Use this when you want the pack base URL as a string.

Important:

- local development: it resolves against your local pack directory
- installed packs: it resolves against the installed pack directory

!!! warning Note

    Assets need to be registered in the `"assets":` array in `widget.json`.
    Trying to grab files from outside registered assets (either file/directory) will result in a 404

### Relative asset URLs

For normal HTML asset attributes, you often do not need `{{ASSETS}}` at all.

These are automatically rewritten against the current pack directory:

- `src`
- `href`
- `poster`
- `srcset`
- inline `style="background-image: url(...)"` values

So this can also work:

```html
<img src="img/logo.png" alt="Logo" />
```

As long as `img/logo.png` exists in the pack at runtime.

### Internal compiler placeholder

You may see `{{pack-install-path}}` in generated output or compiler internals. That is an internal compiler/runtime placeholder, not something pack authors usually need to write directly.

## Widget payload

Your widget receives a payload from DisplayDuck through `WidgetContext`.

Common payload values include:

- `payload.config`: the resolved widget configuration values
- `payload.directory`: the pack directory path

In practice, you usually read config values through helper methods on your widget class.

## WidgetContext

The base runtime gives your widget a `WidgetContext`.

The most important parts are:

- `ctx.mount`: the DOM mount node for your widget
- `ctx.payload`: the current payload and config values
- `ctx.setLoading(boolean)`: show or hide the loading state in the viewer *(this hides your widget entirely)*
- `ctx.on(event, selector, handler)`: delegated event binding

Example:

```ts
public onInit(): void {
  this.ctx.on('click', '#refresh', () => {
    void this.reload();
  });
}
```

## SCSS and CSS variables

At minimum, these variables are already used by the example pack and are safe to document now:

- `--host-width` - The width of your widget within the grid.
- `--host-height` - The height of your widget within the grid.
- `--color-text` - The default text color.
- `--color-primary` - User-selected Accent color.

Example:

```scss
.widget {
  width: calc(var(--host-width) - 0.5em);
  height: calc(var(--host-height) - 0.5em);
  color: var(--color-text);
  border: 1px solid var(--color-primary);
}
```

## Theme variable list

This section is intentionally a starter list and can be expanded later with the full supported theme variables.

Suggested variables to document here over time:

- `--color-primary` - User-selected accent color.
- `--color-text` - The default text color.
- `--color-background` - The background generated from user-selected accent color.
- `--color-background-highlight` - A slighly more saturated and opaque version of `---color-background`
- `--transition` - default transition method and timing

## Notes for pack authors

- Prefer `src/<id>.ts`, `src/<id>.html`, and `src/<id>.scss`
- Keep `dist/` generated and treat it as build output
- Keep non-`dist` files listed in `widget.json.assets` if installed packs need them
