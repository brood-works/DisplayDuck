---
icon: lucide/image
---

# Icons

This page covers which icon sets DisplayDuck expects in pack metadata, and what that means for icons inside your widget UI.

## Pack metadata icons

For icons used in `widget.json`, use classes from:

- Font Awesome Free
- Lineicons Free

This applies to fields such as:

- pack `icon`
- widget `icon`

Example:

```json
{
  "icon": "fas fa-clock",
  "widgets": [
    {
      "category": "clock",
      "icon": "fas fa-clock"
    }
  ]
}
```

These work because those icon fonts are available in the DisplayDuck app UI.

## Icons inside your widget

Those same app icon fonts do **not** automatically work inside your widget UI.

Why:

- packs render inside Shadow DOM
- the app-level icon font styles are not automatically available inside your widget content

So this usually will **not** work inside your pack template just because it works in the main app:

```html
<i class="fas fa-clock"></i>
```

## What to do inside your widget UI

Inside your own widget, you are free to use any icon approach you want.

Common options:

- ship image files like `.png` or `.svg`
- inline SVG directly in your template
- bundle your own icon font or icon library as part of the pack

Example:

```html
<img src="{{ASSETS}}/img/logo.png" alt="Logo" />
```

## Rule of thumb

- use Font Awesome Free or Lineicons Free for `widget.json` metadata icons
- use whatever icon system you want inside the widget itself

That keeps pack metadata compatible with DisplayDuck, while keeping widget rendering fully under your control.
