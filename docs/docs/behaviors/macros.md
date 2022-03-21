---
title: Macro Behavior
sidebar_label: Macros
---

## Summary

The macro behavior allows configuring a list of other behaviors to invoke
when the macro is pressed, including support for completing part of the binding list
once the macro is released.

## Macro Definition

Each macro you want to use in your keymap gets defined first, then bound in your keymap.

A macro definition looks like:

```
/ {
    macros {
        zed_em_kay: zmk_macro {
        label = "ZMK_MAC";
        compatible = "zmk,behavior-macro";
        #binding-cells = <0>;
        bindings
            = <&macro_press &kp LSHFT>
            , <&macro_tap &kp Z &kp M &kp K>
            , <&macro_release &kp LSHFT>
            ;
        };
    };
};
```

:::note
The text before the colon (`:`) in the declaration of the macro node is the "node label", and is the text
used to reference the macro in your keymap
:::

The macro can then be bound in your keymap by referencing it by the label `&zed_em_kay`, e.g.:

```
        raise_layer {
            bindings = <&zed_em_kay>;
        };
```

### Bindings

Like [hold-taps](/docs/behaviors/hold-tap), macros are created by composing other behaviors, and any of those behaviors can
be added to the `bindings` list, e.g.:

```
bindings
    = <&to 1>
    , <&bl BL_ON>
    , <&kp Z &kp M &kp K &kp EXCLAMATION>
    ;
```

## Macro Controls

There are a set of special macro controls that can be included in the `bindings` list to modify the
way the macro is processed.

### Binding Activation Mode

Bindings in a macro are activated differently, depending on the current "activation mode" of the macro.

Available modes:

- Tap - The default mode; when in this mode, the macro will press, then release, each behavior in the `bindings` list. This mode is useful for
  basic keycode output to hosts, i.e. when activating a `&kp` behavior.
- Press - In this mode, the macro processing will only trigger a press on the behaviors in the macro. This is useful for holding down modifiers for some duration of a macro, e.g. `&kp LALT`.
- Release - In this mode, the macro processing will only trigger a release on the behaviors in the macro. This is useful for releasing modifiers previously pressed earlier in the macro processing, e.g. `&kp LALT`.

To modify the activation mode, macro controls can be added at any point in the bindings list.

- `&macro_tap`
- `&macro_press`
- `&macro_release`

A concrete example, used to hold a modifier, tap multiple keys, then release the modifier, would look like:

```
bindings
    = <&macro_press &kp LSHFT>
    , <&macro_tap &kp Z &kp M &kp K>
    , <&macro_release &kp LSHFT>
    ;
```

### Processing Continuation on Release

The macro can be paused so that only part of the `bindings` list is processed when the macro is pressed, and the remainder is processed once
the macro itself is released.

To pause the macro until release, use `&macro_pause_for_release`, like in this example that will press a modifier and activate a layer when the macro is pressed, and once the macro is released, release the modifier and deactivate the layer by releasing the `&mo`:

```
bindings
	= <&macro_press &mo 1 &kp LSHFT>
	, <&macro_pause_for_release>
	, <&macro_release &mo 1 &kp LSHFT>
	;
```

### Wait Time

The wait time setting controls how long of a delay is introduced between behaviors in the `bindings` list. The initial wait time for a macro, 100ms by default, can
be set by assigning a value to the `wait-ms` property of the macro, e.g. `wait-ms = <20>;`. If you want to update the wait time at any
point in the macro bindings list, use `&macro_wait_time`, e.g. `&macro_wait_time 30`. A full example:

```
wait-ms = <10>;
bindings
    = <&kp F &kp A &kp S &kp T>
    , <&macro_wait_time 500>
    , <&kp S &kp L &kp O &kp W>
    ;
```

### Tap Time

The tap time setting controls how long a tapped behavior is held in the `bindings` list. The initial tap time for a macro, 100ms by default, can
be set by assigning a value to the `tap-ms` property of the macro, e.g. `tap-ms = <20>;`. If you want to update the tap time at any
point in a macro bindings list, use `&macro_tap_time`, e.g. `&macro_tap_time 30`. A full example:

```
bindings
    = <&macro_tap_time 10>
	, <&kp S &kp H &kp O &kp R &kp T>
    , <&macro_tap_time 500>
    , <&kp L &kp O &kp N &kp G>
    ;
```
