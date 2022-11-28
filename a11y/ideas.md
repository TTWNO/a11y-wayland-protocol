# Accessibility Protocol for Wayland (a11y)

This will bring Linux up to par with major operating systems like MacOS and Windows.
Implementation is entirely unknown.
But this is what I'd like to see:

Note that this is a DRAFT, and implementation details, simplicity, knowledge of what belongs in its own standard, etc. are not known at this time.
This is more ideas than anything concrete.

## Calls

### notify

There are a few ideas here about being able to use accessibility notifiers like flashing or sending a pulse out from a cursor (as done by Cinnamon) and displaying the currently selected text larger and above the current window.

### filter

```c
bool filter(FILTER_MODE,
	surface_id|rect,
	[options ...]
);
```

`FILTER_MODE` options:

* `Greyscale`
	* Any color where all RGB values are equal.
* `BlackAndWhite`
	* Either #FFFFFF or #000000
* `Invert`
	* Invert RGB values.
* `Kernel (-1 to 1, ...x9)`
	* For example, blur, edge detection, etc.
* `Rgb (-255 to 255, ...x3)`
	* For color-blindness filters. Change color by a fixed amount per RGB value.

Many of these filters may be stacked.
For example, inverted + yellow color-blindness filter.
This also allows filtering per a rect or surface (window) individually, not just the entire screen.

### crop

```c
bool crop(surface_id|rect, X, Y, W, H);
```

This will show only the content within the bounds scaled to the proper size.
To avoid the font-stair-stepping problem, users who use this feature may want to turn on HiDPI rendering at the same time (i.e., over-scaling).
The reason users on MacOS do not have this problem is because Apple products have such a high pixel density.
On older devices with larger screens, you may still notice the stair-stepping problem.
Since new devices have such high PPI, it only makes sense that this is somewhat avoided.

Some other options might be interesting, for example being able to crop relative to a window and also relative to the entire screen if needed.

### input

Assistive technology often requires exclusive use of the keyboard, mouse, or other peripherals.
This section will need to implement a few protocols to allow assistive technology to take advantage of hardware.

#### new_keybind

```rust
/* this will send an event to the client when the requested key combo has been typed */
bool new_keybind(
	passthrough: bool, /* does the key binding continue down the stack or get consumed */
	after: bool, /* activate when key is released */
	keys: Vec<Key>, /* list of keys that must be pressed in this order to be triggered */
);
```

```rust
/* request control of input devices directly.
	This should provide a keyboard, mouse, etc. with direct control from an application.
	All key presses, releases, mouse clicks, etc. will need to be initiated from the requesting application from now on.
*/
bool exclusive_key_daemon(
	seat: seat_id, /* input seat */
);
```

```rust
/* send key info from exclusive control process */
bool send_input_event(
	seat: seat_id,
	event_type: click|double_click|etc.
);
```


