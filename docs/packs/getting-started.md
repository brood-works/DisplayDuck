---
icon: lucide/package-plus
---

# Create a DisplayDuck pack

This guide uses the example pack in `packs-local/displayduck-pack-example` as the starting point.

In practice, this guide is about building a standalone DisplayDuck widget pack.

## Start from the example

The fastest way to get moving is to copy [the example pack](https://github.com/pondhub-io/displayduck-pack-example) and rename it:

```text
your-pack/
  package.json
  widget.json
  src/
    your-widget.ts
    your-widget.html
    your-widget.scss
```

The compiler reads from `src/` and writes the built widget module to `dist/`.

!!! warning Note

    `src/` and `dist/` are static paths that need to exist in your widget pack for your widget to work.

## Required files

### `package.json`

Use this shape as a baseline:

```json
{
  "scripts": {
    "build": "displayduck-compile-pack --pack-dir .",
    "development": "displayduck-compile-pack --pack-dir . --watch"
  },
  "devDependencies": {
    "@displayduck/base": "^0.1.0",
    "@displayduck/compiler": "^0.1.0"
  },
  "main": "./dist/example.js"
}
```

What each field is for:

- `scripts.build`: builds the pack once into `dist/`
- `scripts.development`: keeps rebuilding while you edit files in `src/`
- `@displayduck/base`: the runtime helpers and widget API
- `@displayduck/compiler`: the pack compiler CLI
- `main`: points at the built widget entrypoint that DisplayDuck should load 

!!! warning Note

    You can only have one `.js` output per pack.

### `widget.json`

This is the pack manifest DisplayDuck reads.

```json
{
  "icon": "fas fa-bug",
  "name": "Example Widget",
  "description": "This is an example widget for development purposes.",
  "assets": ["src/img"],
  "version": "0.0.1",
  "widgets": [
    {
      "category": "utility",
      "id": "example",
      "icon": "fas fa-code",
      "name": "Example Widget",
      "description": "This is an example widget for development purposes",
      "platform": "all",
      "minWidth": 200,
      "minHeight": 100,
      "config": {}
    }
  ]
}
```

What the top-level fields do:

- `icon`: the pack icon shown in pack lists (See [Icons](icons)) for details)
- `name`: the pack name shown in DisplayDuck
- `description`: the pack-level summary
- `version`: the pack version
- `assets`: extra files or directories to copy into installed packs when they are not part of `dist/`
- `widgets`: the widgets this pack provides

What each widget entry needs:

- `id`: the widget entrypoint id; the compiler emits `dist/<id>.js`
- `category`: the widget category
- `icon`: the widget icon shown in DisplayDuck
- `name`: the widget display name
- `description`: the widget summary shown in the picker
- `platform`: `all`, `windows`, `macos`, `linux`, or an array of those values
- `minWidth` and `minHeight`: the widget’s minimum size (in pixels) in the layout editor
- `config`: configuration fields shown in the widget settings UI

Supported categories:

- `audio`
- `clock`
- `control`
- `feed`
- `game`
- `media`
- `metric`
- `other`
- `social`
- `status`
- `utility`
- `weather`

## Supported config field types

DisplayDuck currently accepts these config field types:

- `string`
- `number`
- `boolean`
- `select`
- `widget`
- `color-picker`
- `hidable-widget`

Each config field can also use:

- `label`
- `subLabel`
- `defaultValue`
- `hidden`
- `options`
- `min`
- `max`

Example:

```json
{
  "accentColor": {
    "label": "Accent color",
    "type": "color-picker",
    "defaultValue": "#ffffff"
  },
  "layout": {
    "label": "Layout",
    "type": "select",
    "options": ["compact", "full"],
    "defaultValue": "compact"
  }
}
```

## Source files

For a widget with `id: "example"`, the compiler looks for:

- `src/example.ts`
- `src/example.html`
- `src/example.scss`

Your TypeScript class usually looks like this:

```ts
import { signal, type Signal, type WidgetContext } from '@displayduck/base';

export class DisplayDuckWidget {
  public showConfig: Signal<boolean>;

  public constructor(private readonly ctx: WidgetContext) {
    this.showConfig = signal(false);
  }

  public onInit(): void {
    this.ctx.on('click', '#btn', () => {
      this.showConfig.set(!this.showConfig());
    });
  }
}
```

## Assets

If your widget needs files outside `dist/`, keep them in the pack and list them in `widget.json.assets`.

Examples:

- `"assets": ["img"]`
- `"assets": ["src/img"]`
- `"assets": ["data/config.json"]`

Why this matters:

- local development reads directly from your pack directory, so local files are available
- installed packs only get non-`dist` files that are explicitly copied during install
- today, `assets` is the mechanism that tells DisplayDuck what extra files to include

If you reference `{{ASSETS}}/path/file.json` in a template, that path must still exist in the installed pack.

## Build output

The compiler writes your built widget module to `dist/`.

Examples:

- widget id `example` -> `dist/example.js`
- widget id `weather` -> `dist/weather.js`

DisplayDuck loads the built module from `dist`, not from your source files.
