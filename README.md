# command-auto-yee

> Auto-wire `command=` and `commandable=` elements together, implemented as [`yee`](https://github.com/rektide/yee) appliers.

`command-auto-yee` is a port of the [`command-auto`](https://github.com/rektide/command-auto) concept — a top-level mixin that pairs elements looking for commands (`commandable=`) with elements offering them (`command=`) — into the yee mixing/remixing architecture.

Where the original `command-auto` sketched a bespoke `<command-auto>` mixcomp that lived on `<body>` and managed wirings itself, `command-auto-yee` is expressed as a `<yee-mix>` host whose children are yee appliers. yee owns target discovery and apply/unapply bookkeeping; `command-auto-yee` only contributes the wiring logic.

## Architecture

### Source concepts (unchanged from `command-auto`)

- **`command=`** — an HTML element advertising that it offers a named command.
- **`commandable=`** — an element looking for one or more `command=` elements to satisfy it.
- **Multi-commandables** — an element that accepts several commands (e.g. a `<video>` with `play`, `pause`, `toggle-muted`). Carried over from [Invoker Commands Futures](https://open-ui.org/components/future-invokers.explainer/).

### yee formulation

A top-level controller mix on `<body>`:

```html
<body>
  <yee-mix mix="body > *">
    <command-auto-applier></command-auto-applier>
    <command-multier-applier></command-multier-applier>
  </yee-mix>

  <button command="play">▶</button>
  <video commandable="play,pause,toggle-muted"></video>
</body>
```

`<yee-mix>` matches every top-level child (or whatever selector is given) and runs each applier against it. Appliers react to `command=` / `commandable=` attributes on the matched target.

## Components

### `command-auto-applier`

A yee applier that, for each target, scans its subtree (or itself) for unsatisfied `commandable=` attributes and assigns matching `command=` elements. Re-evaluates on yee's live target stream.

### `command-multier-applier`

A yee applier that splits comma-separated `command=`/`commandable=` attributes into individual per-command mix records, so each command can be wired and unwired independently. Roughly the yee analogue of the original `command-multi` element.

### `command-auto-controller` / `command-multier-controller`

Optional top-level observers (still singletons on `<body>`) that watch the document for newly added `command=` / `commandable=` elements and trigger their containing `<yee-mix>` to re-bind. These exist because target discovery may need to look beyond what `<yee-mix>`'s selector initially matched.

## Status

Baseline / architecture sketch. Applier implementations pending; see beads tickets under prefix `comay`.

## Related

- [`yee`](https://github.com/rektide/yee) — the mixing/remixing webcomponent architecture this targets.
- [`command-auto`](https://github.com/rektide/command-auto) — the original mixcomp formulation.
- [`mixcomp`](https://github.com/rektide/mixcomp) — the mixin-component architecture kit that spawned both.

## License

MIT
