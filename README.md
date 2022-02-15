# GMMK Pro - QMK and VIA RGB Guide macOS

**0 responsibility on my part if you brick your board**

This guide is mostly for the peeps who want more customizability in configuring their qmk map, if you just want to change rgb with keycodes you can use the [qmk configurator](https://config.qmk.fm/#/gmmk/pro/ansi/LAYOUT).

---

## Instal qmk_toolbox

For macOS, select the "QMK.Toolbox.pkg" from [here](https://github.com/qmk/qmk_toolbox/releases).

In terminal:

```
qmk setup
```

Once the setup is complete, go to `C:\users\your username\qmk_firmware`. Do this in terminal.

Once you are in the `qmk_firmware` directory, end this into terminal.

```
qmk config user.keyboard=gmmk/pro/ansi
```

If you're using the ISO version of the gmmk pro, paste this instead:

```
qmk config user.keyboard=gmmk/pro/iso
```

It will look like nothing was done, but that is correct. We will now create a custom keymap with this command:

```
qmk config user.keymap=<your keymap name>
```

For example, I renamed the folder to "peep", therefore I enter...

```
qmk config user.keymap=peep
```

---

## Editing the Keymap

Open the keymap folder you previously renamed. In my case, "peep".

In that folder you will find a filed named `keymap.c`. DO NOT RENAME THIS.

This is a file written in C and will be used to edit your keymap.

Helpful keycodes can be found [here](https://docs.qmk.fm/#/keycodes)

Code for rotary encoders can be found [here](https://docs.splitkb.com/hc/en-us/articles/360010513760-How-can-I-use-a-rotary-encoder)

**Do not forget to set a reset button if you have removed it from the keymap,standard is layer 1 pipe key/backslash (\|)**

- If you want to add additional layers, you can paste this into the 'keymap.c' file under the [0] = LAYOUT.

```
//ANSI
[X] = LAYOUT(
        _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______,          _______,
        _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______,          _______,
        _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______,          _______,
        _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______,          _______,          _______,
        _______,          _______, _______, _______, _______, _______, _______, _______, _______, _______, _______,          _______, _______, _______,
        _______, _______, _______,                            _______,                            _______, _______, _______, _______, _______, _______
    ),
```

```
// ISO
[X] = LAYOUT(
        _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______,          _______,
        _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______,          _______,
        _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______,                   _______,
        _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______,          _______,
        _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______,          _______, _______, _______,
        _______, _______, _______,                            _______,                            _______, _______, _______, _______, _______, _______
    ),
```

- When switching to a layer make sure the key you are using as a layer switch(MO for example) is kc_trns or **\_\_**, on the layer you are switching to in the keymap:

```
[0] = LAYOUT(
        _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______,          _______,
        _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______,          _______,
        _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______,          _______,
        _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______,          _______,          _______,
        _______,          _______, _______, _______, _______, _______, _______, _______, _______, _______, _______,          _______, _______, _______,
        _______, _______, _______,                            _______,                            _______, MO(1),   _______, _______, _______, _______
    ),                                                                                                     ^^layer switch

    [1] = LAYOUT(
        _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______,          _______,
        _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______,          _______,
        _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______,          _______,
        _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______, _______,          _______,          _______,
        _______,          _______, _______, _______, _______, _______, _______, _______, _______, _______, _______,          _______, _______, _______,
        _______, _______, _______,                            _______,                            _______, _______, _______, _______, _______, _______
    ),                                                                                                     ^^transparant key
```

As you can see the **last key** in the keymap does **NOT** require a comma at the end, please refrain from adding one as that tells the compiler that there's another key coming up!

### Rotary Knob

Add the following code below the layouts section

```
#ifdef ENCODER_ENABLE
bool encoder_update_user(uint8_t index, bool clockwise) {
    if (clockwise) {
      tap_code(KC_VOLU);
    } else {
      tap_code(KC_VOLD);
    }
    return true;
}
#endif // ENCODER_ENABLE
```

### LED - Capslock, Numlock and Scroll Lock Indicators

Paste this in the 'keymap.c' under the layout and knob:

```
void rgb_matrix_indicators_advanced_user(uint8_t led_min, uint8_t led_max) {
  if (IS_HOST_LED_ON(USB_LED_CAPS_LOCK)) {
    RGB_MATRIX_INDICATOR_SET_COLOR(index, R, G, B); //capslock key
  }
  if (IS_HOST_LED_ON(USB_LED_SCROLL_LOCK)) {
	  RGB_MATRIX_INDICATOR_SET_COLOR(index, R, G, B); // scroll lock key
  }
  if (!IS_HOST_LED_ON(USB_LED_NUM_LOCK)) {   // on if NUM lock is OFF
    RGB_MATRIX_INDICATOR_SET_COLOR(index, R, G, B);
  }
}
```

You can enter the values at **index, R, G, B** to target individual LEDs and their colors.

- index: index number used to target the LED.
- R, G, B: change values to change color.

Here is the layout of the leds (index numbers) on the board FOR ANSI:

```
// RGB LED layout

// led number, function of the key

//  67, Side led 01    0, ESC      6, F1       12, F2       18, F3       23, F4       28, F5       34, F6       39, F7       44, F8       50, F9       56, F10      61, F11      66, F12      69, Prt       Rotary(Mute)   68, Side led 12
//  70, Side led 02    1, ~        7, 1        13, 2        19, 3        24, 4        29, 5        35, 6        40, 7        45, 8        51, 9        57, 0        62, -_       78, (=+)     85, BackSpc   72, Del        71, Side led 13
//  73, Side led 03    2, Tab      8, Q        14, W        20. E        25, R        30, T        36, Y        41, U        46, I        52, O        58, P        63, [{       89, ]}       93, \|        75, PgUp       74, Side led 14
//  76, Side led 04    3, Caps     9, A        15, S        21, D        26, F        31, G        37, H        42, J        47, K        53, L        59, ;:       64, '"                    96, Enter     86, PgDn       77, Side led 15
//  80, Side led 05    4, Sh_L     10, Z       16, X        22, C        27, V        32, B        38, N        43, M        48, ,<       54, .<       60, /?                    90, Sh_R     94, Up        82, End        81, Side led 16
//  83, Side led 06    5, Ct_L     11,Win_L    17, Alt_L                              33, SPACE                              49, Alt_R    55, FN                    65, Ct_R     95, Left     97, Down      79, Right      84, Side led 17
//  87, Side led 07                                                                                                                                                                                                        88, Side led 18
//  91, Side led 08                                                                                                                                                                                                        92, Side led 19
```

For example, to target the LED under the capslock and make it red, the code would look like this:

```
void rgb_matrix_indicators_advanced_user(uint8_t led_min, uint8_t led_max) {
	if (IS_HOST_LED_ON(USB_LED_CAPS_LOCK)) {
        RGB_MATRIX_INDICATOR_SET_COLOR(3, 255, 0, 0); //capslock key
    }
}
```

`3` is the index
`255, 0, 0` is red

This is the **ISO map**:

```
// RGB LED layout ISO

// led number, function of the key

//  68, Side led 01    0, ESC      6, F1       12, F2       18, F3       23, F4       28, F5       34, F6       39, F7       44, F8       50, F9       56, F10      61, F11      66, F12      70, Prt       			   Rotary(Mute)   69, Side led 12
//  71, Side led 02    1, `~       7, 1        13, 2        19, 3        24, 4        29, 5        35, 6        40, 7        45, 8        51, 9        57, 0        62, -_       79, (=+)     86, BackSpc   		     73, Del        72, Side led 13
//  74, Side led 03    2, Tab      8, Q        14, W        20. E        25, R        30, T        36, Y        41, U        46, I        52, O        58, P        63, [{       90, ]}                     			   76, PgUp       75, Side led 14
//  77, Side led 04    3, Caps     9, A        15, S        21, D        26, F        31, G        37, H        42, J        47, K        53, L        59, ;:       64, '"       95, #        97, Enter     			   87, PgDn       78, Side led 15
//  81, Side led 05    4, Sh_L     67,\		     10, Z        16, X        22, C        27, V        32, B        38, N        43, M        48, ,<       54, .<       60, /?             		    91, Sh_R     94, Up    83, End        82, Side led 16
//  84, Side led 06    5, Ct_L     11,Win_L    17, Alt_L                              			   33, SPACE                              	  49, Alt_R    55, FN       65, Ct_R                  96, Left     98, Down  80, Right      85, Side led 17
//  88, Side led 07                                                                                                                                                                                                        		    	 89, Side led 18
//  92, Side led 08                                                                                                                                                                                                        			     93, Side led 19
```

---

## VIA

**Adding VIA support to your qmk keymap**

Copy the file named `rules.mk` in the `qmk_firmware\keyboards\gmmk\pro\ansi` to the folder of the keymap you are making. In my case, to the folder name "peep". Remember, this is located in the `qmk_firmware\keyboards\gmmk\pro\ansi\keymaps` folder.

For ISO, `qmk_firmware\keyboards\gmmk\pro\iso`.

Add the following on the last line in the `rules.mk` file you copied to the folder of your keymap:

```
VIA_ENABLE = yes
```

## Compile the Keymap

When you are done editing the keymap, save it and type this in to Terminal:

```
qmk compile
```

It will compile the keymap and put it in the `qmk_firmware` folder.

The new file will be called something like this: "gmmk_pro_ansi_peep.bin"

## Flashing the Keyboard

Launch QMK Toolbox.

Drag your newly created "gmmk_pro_ansi_peep.bin" into the input area beside the "open" button. This is the path to your ""gmmk_pro_ansi_peep.bin" file.

Hold spacebar+b on your GMMK Pro while plugging in the usb c cable, this should put it into the bootloader mode.

Now click flash on the top right and it will start flashing the firmware onto the keyboard.

It is very important that you do not change anything about the power or data delivery towards the keyboard until it is done flashing, so do not remove the cable, do not turn off your computer, do not flip the switch on your power strip, do not close the program etc.

Done!
