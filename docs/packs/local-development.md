---
icon: lucide/laptop
---

# Local development

This is the fastest way to work on a pack while DisplayDuck is running locally.

## 1. Create or copy a pack

The easiest path is to copy:

```text
packs-local/displayduck-pack-example
```

Rename the folder, update `widget.json`, and rename the `src/example.*` files to match your widget id.

## 2. Install dependencies

Inside your pack directory:

```bash
npm install
```

Your `package.json` should include:

```json
{
  "devDependencies": {
    "@displayduck/base": "^0.1.0",
    "@displayduck/compiler": "^0.1.0"
  }
}
```

## 3. Start the compiler in watch mode

```bash
npm run development
```

This watches your pack source files and rebuilds `dist/<id>.js` when they change.

## 4. Add the pack location to DisplayDuck

DisplayDuck discovers local packs from configured local pack paths.

Use the Widgets tab in DisplayDuck and add the parent folder that contains your pack, for example:

```text
.../DisplayDuck/packs-local
```

That allows DisplayDuck to discover packs inside that directory.

## 5. Turn on developer mode for live refresh and logs

DisplayDuck only watches local pack output for live refresh when developer mode is enabled.
When developer mode is enabled, right click on the tray icon to toggle the logs window.

So for the smoothest loop:

- start DisplayDuck in developer mode
- add your local pack path
- run `npm run development`
- edit files in `src/`

## Typical workflow

```bash
cd packs-local/displayduck-pack-example
npm install
npm run development
```

Then:

- edit `src/example.ts`
- edit `src/example.html`
- edit `src/example.scss`
- watch the compiler rebuild `dist/example.js`
- DisplayDuck reloads the local pack output in developer mode

## Troubleshooting

### The widget does not appear

Check:

- the pack folder contains `widget.json`
- the configured local pack path points at the parent directory of the pack
- the widget id matches the source file names

For `id: "example"`, the compiler expects:

- `src/example.ts`
- `src/example.html`
- `src/example.scss`

### Assets work locally but fail after install

That usually means the files exist in your working directory, but were not copied into the installed pack.

Make sure `widget.json.assets` includes the non-`dist` files or directories you need.

### The compiler rebuilds, but DisplayDuck does not refresh

Check:

- developer mode is enabled in DisplayDuck
- the compiler is writing to `dist/`
- the built file name matches the widget id

## Recommended structure

```text
my-pack/
  package.json
  widget.json
  src/
    my-widget.ts
    my-widget.html
    my-widget.scss
    img/
      logo.png
```

This is a good starting point because source, templates, and local assets stay together.
