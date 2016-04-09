# hax11

Hackbrary to **H**ook and **A**ugment **X11** protocol calls.

Attempts to fix game and full-screen application issues on Linux, such as:
- starting on the wrong monitor
- spanning too many monitors
- spanning one half of a 4K MST monitor
- refusing to allow selecting a desired resolution

## Building

Build the library:
```bash
make
```

The Makefile assumes you have a 64-bit system. You will need gcc-multilib to build the 32-bit version.

## Usage

To try this library, run the following in a shell:

```
export LD_PRELOAD=`pwd`/\$LIB/hax11.so
```

Then, from the same shell session, start the desired game or application.

## Configuration

By default, this library will not do anything.

For every application using the affected API, it will create an empty configuration file
under `$HOME/.config/hax11/profiles/`. Each file corresponds to one program, and will be named
after the program executable's absolute path, but with forward slashes `/` substituted with backslashes `\`.

Additionally, a `default` configuration file (in the same directory as above) will be loaded before the program's.

The syntax is one `Name=Value` pair per line.

Supported configuration options:

Name                  | Values  | Description
--------------------- | ------- | ---------------------------------
`Enable`              | `0`/`1` | Boolean - Intercept the X11 connection (required for any other settings to have any effect)
`JoinMST`             | `0`/`1` | Boolean - Join MST panels and present them as one monitor to the application
`MaskOtherMonitors`   | `0`/`1` | Boolean - Whether to hide the presence of other monitors from the application
`ResizeWindows`       | `0`/`1` | Boolean - Whether to forcibly change the size of windows that span too many monitors
`ResizeAll`           | `0`/`1` | Boolean - Resize (stretch) all windows, not just those matching the size of one MST panel
`MoveWindows`         | `0`/`1` | Boolean - Whether to forcibly move windows created at (0,0) to the primary monitor
`MainX`/`Y`           | Number  | The X11 coordinates of your primary monitor (or left-top-most monitor to be used for games)
`MainW`/`H`           | Number  | The resolution of your primary monitor (or total resolution of monitors to be used for games)
`DesktopW`/`H`        | Number  | The resolution of your desktop (all monitors combined)
`Debug`               | Number  | Log level - Non-zero enables debugging output to stderr and `/tmp/hax11.log`

A sensible configuration is to have `JoinMST=1` and the `Main*` / `Desktop*` settings in the `default` profile,
and per-game settings in their executables' profiles.

Beware that your window manager and shell use the same APIs,
thus having other options enabled for such programs may make other monitors unusable.

To temporarily disable hax11, unset `LD_PRELOAD` before running a program, e.g.:
```
$ LD_PRELOAD= xrandr
```

## Installation

You can install this library system-wide, so that it is loaded into all applications on start-up.

**Warning**! This project may or may not be compatible with your system.
Before installation, make sure you're capable to uninstalling this library if X, your desktop environment, etc. refuses to start.

This will install the libraries under `/usr/local/lib{32,64}`, and a script under `/etc/profile.d`:
```bash
make install
```

Log out and back in to apply the hack to all applications.

## Status

The following table represents the results of testing this library on the author's machine,
consisting of a 1920x1200 monitor at (0,0), and a 4K monitor in MST mode (thus presenting itself as two 1920x2160 panels) at (1920,0).

The desired result (and solution listed to achieve said result) is to launch the game in full-screen on the 4K monitor,
at 4K resolution if the game supports it.

Game                            | Status
------------------------------- | -----------------------------------------------
10,000,000                      | Works (`MoveWindows` + `ResizeWindows`)
140                             | Works (Unity - `MoveWindows`)
3089                            | Java. Resolution selector is completely broken, throws NullPointerException
Adventures of Shuggy            | Works (not needed)
And Yet It Moves                | Works (`MoveWindows` + `ResizeWindows`, then set `<Resolution>`...`</Resolution>` in `~/.Broken Rules/And Yet It Moves/common/commonConfig.xml`)
Anodyne                         | Adobe Air version doesn't start (exits with code 5); Standalone (.swf) version can't get past the calibration screne
Antichamber                     | Works (not needed)
Aquaria (Steam)                 | Works (`ResizeWindows`, then set `resx` and `resy` in `~/.Aquaria/preferences/usersettings.xml`)
Aquaria (tarball)               | Works (as above)
Avadon: The Black Fortress      | Works (`MoveWindows` + `ResizeWindows`)
Bad Hotel                       | Works (`ResizeWindows` + `ResizeAll`)
The Binding of Isaac: Rebirth   | Works (not needed)
BIT.TRIP RUNNER                 | Does not actually have a Linux port on Steam
Blueberry Garden                | Works (`ResizeWindows`)
Bridge Constructor Playground   | Works (Unity - `MoveWindows`)
Card City Nights                | Works (Unity - `MoveWindows`)
Crimsonland                     | Works (`ResizeWindows`, then change the resolution)
Darwinia                        | Works (`MoveWindows` + `ResizeWindows`, change the resolution to fix AR)
DEFCON                          | Works (`MoveWindows` + `ResizeWindows`, then change the resolution)
Dota 2                          | Works (`ResizeWindows` + "Use my monitor's current resolution")
Dungeons of Dredmor             | Works (`MoveWindows` + `ResizeWindows`, set resolution in the launcher)
Dust: An Elysian Tail           | Works (`ResizeWindows`, then change the resolution)
Dynamite Jack                   | Works (`MoveWindows` + `ResizeWindows`, then change the resolution)
Escape Goat                     | Works (`ResizeWindows` + `ResizeAll`)
Eversion                        | Works (not needed)
Faerie Solitaire [Beta]         | Works (`ResizeWindows`)
Garry's Mod                     | Works (Source - `ResizeWindows`, then change the resolution)
Gigantic Army                   | Works (not needed)
Gone Home                       | Doesn't start at all on my machine
Guacamelee! Gold Edition        | Works (`ResizeWindows`, then change the resolution)
Half-Life 2: Deathmatch         | Works (Source - `ResizeWindows`, then change the resolution)
Heavy Bullets                   | Works (not needed)
Hexcells                        | Works (Unity - `MoveWindows`)
HyperRogue                      | Works (`MoveWindows` + `ResizeWindows`, then set the resolution on the first line of `~/.hyperrogue.ini`)
Intrusion 2                     | Works (not needed)
Jazzpunk                        | Works (`MoveWindows`)
Lugaru HD                       | Works (`ResizeWindows`, then set the resolution in `~/.lugaru/Data/config.txt`)
Lume                            | Works (`ResizeWindows`, then `Ctrl+F` to exit full screen, drag to correct monitor, `Ctrl+F` to enter full screen)
The Magic Circle                | Works (`MoveWindows`)
Multiwinia                      | Crashes after the splash screen on my machine
Osmos                           | Works (not needed)
Outlast                         | Works (`ResizeWindows`, then change the resolution)
Papers, Please                  | Works (not needed)
Perfection.                     | TODO - Queries resolution via proprietary NVIDIA protocol extension
Psychonauts                     | Works (`MoveWindows` + `ResizeWindows`, then set the resolution on the first line of `$XDG_DATA_HOME/Psychonauts/DisplaySettings.ini`)
Quest of Dungeons               | Works (`ResizeWindows`)
Risk of Rain                    | Works (not needed)
Satazius                        | Works (not needed)
Shatter                         | Works (`ResizeWindows`)
Sigils of Elohim                | Doesn't seem to have a full-screen mode. Works otherwise
Snuggle Truck                   | Works (Unity - `MoveWindows`)
Soul Axiom                      | Works (Unity - `MoveWindows`)
Starbound                       | Works (`MoveWindows` + `ResizeWindows`)
Sunless Sea                     | Works (`MoveWindows` + `ResizeWindows` + `ResizeAll`, then set the resolution to 1920x1080)
Super Hexagon                   | Works (`MoveWindows` + `ResizeWindows`)
Superfrog HD                    | TODO - Wrong scaling
Teleglitch: DME                 | Works (not needed)
Terraria                        | Works (not needed)
Teslagrad                       | Works (Unity - `MoveWindows`)
They Bleed Pixels               | Works (`ResizeWindows`)
Tiki Man                        | Works (Unity - `MoveWindows`)
TIS-100                         | Works (`MoveWindows`)
Uplink                          | Works (`MoveWindows` + `ResizeWindows` + `ResizeAll`)
Voxatron                        | Works (`ResizeWindows`), though the KDE panel remains on top for some reason.
VVVVVV                          | Works (not needed)
World of Goo                    | Works (`MoveWindows` + `ResizeWindows`, then copy `.../World of Goo/properties/config.txt` to `~/.WorldOfGoo/config.txt` and set `screen_width`/`screen_height`)

## License

Copyright (c) 2015-2016 hax11 authors.

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.