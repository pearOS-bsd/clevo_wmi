# clevo_dchu — keyboard backlight for Clevo / Tongfang laptops on FreeBSD

FreeBSD kernel module for the keyboard backlight of Clevo / Tongfang laptops that expose the **DCHU** ACPI device (`CLV0001`), such as the NS50_70MU (BIOS 1.07.09LWL). (The repository is named `clevo_wmi`, but the controls are not WMI: the BIOS offers them as the `_DSM` method of `\_SB.DCHU`, which is what this driver calls. A WMI attempt with a made-up GUID was dropped.)

`_DSM` UUID `93f224e4-fbdc-4bbf-add6-db71bdc0afad`, revision 0; the function number is the command, the argument a package holding a 4-byte buffer:

| function | argument | effect |
|---|---|---|
| 0x67 | `0xF4000000 \| level` | brightness 0 ... 255 |
| 0x67 | `0xF0RRGGBB` / `0xF1RRGGBB` / `0xF2RRGGBB` | colour of zone 0 (left) / 1 (middle) / 2 (right) |

Found in the DSDT of the NS50_70MU and tried from Linux with `acpi_call` before the driver was written. Verified on that laptop: brightness works. Zone colours: the driver sends the commands; tell us if your model differs.

## sysctls (writable by any user: `CTLFLAG_ANYBODY`)

    dev.clevo_dchu.0.kb_brightness   0 ... 255
    dev.clevo_dchu.0.kb_state        0 off, 1 on
    dev.clevo_dchu.0.kb_color        0xRRGGBB for all three zones

## Build and load

    make SYSDIR=/usr/src/sys
    kldload acpi_wmi        # not needed by the _DSM path on all machines; pearOS loads it first
    kldload ./clevo_dchu.ko
    sysctl dev.clevo_dchu.0.kb_brightness=255

Used by [pearOS](https://github.com/pearOS-bsd/misc) (Control Center slider, boot-time light on).

License: see the SPDX header of `clevo_dchu.c` (BSD-2-Clause if absent).
