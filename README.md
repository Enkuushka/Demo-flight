# Patrol

A pixel-art fighter-jet flying toy over autumn lake country at the last light of day.
Everything is in one self-contained file, `index.html`: raw WebGL2 for the world, Canvas 2D for
the cockpit, and every image drawn by code. The only external resources are two Google Fonts.

Open `index.html` in a current desktop or mobile browser that supports WebGL2.

## Controls

| Input | Action |
| --- | --- |
| Mouse | Steer. In the top view the jet flies toward the cursor; in the chase and cockpit views the cursor's offset from centre sets turn and climb or dive |
| Click | Barrel roll |
| Hold left button | Afterburner |
| Right click or `F` | Fire a missile (four on the wings, each re-arms after a few seconds) |
| `Space` | Cycle views: top-down (north up), chase, cockpit |
| `M` | Sound on or off (off by default) |
| `1` `2` `3`, `R`, `Shift` | Direct view select, roll, afterburner |

On touch screens, drag anywhere to steer. The on-screen buttons are BURN, ROLL, FIRE and VIEW.

The jet cannot crash. It pulls up on its own before terrain, and it flies lazy S-turns until you
first move the mouse.

## How it works

- **World**: web workers generate a seamlessly tiling 2048² heightfield (8 km), forest and meadow
  maps, and a field of flat-based cumulus domes while a progress bar runs. Long sun shadows are
  baked with a single diagonal sweep. A fragment shader ray-marches terrain, water and clouds per
  pixel, so any camera angle works.
- **Look**: the scene renders at about 340 px tall and is upscaled by an integer factor with
  nearest-neighbour filtering. A post pass draws the jet's 1 px outline and maps every pixel to a
  fixed palette of about 100 dusk colours. It dithers between the nearest palette pair with a 4×4
  Bayer matrix; those pairs come from a 64³ lookup built on the GPU at startup.
- **Cockpit**: every instrument is plotted pixel by pixel into an `ImageData` buffer, and every
  reading comes from the simulation: airspeed, attitude, altimeter, turn and slip, heading,
  vertical speed, ground-mapping radar, engines, local-time clock, warning lamps and throttle. The
  HUD pitch ladder is projected through the same camera matrix as the world, so it sits exactly on
  the real horizon.
- **Performance**: the internal resolution steps down automatically when frames get slow.

## URL parameters

- `?seed=1234`: generate a different world
- `?res=300`: lock the internal resolution (disables the automatic adjustment)
- `?test`: exposes `window.PATROL` hooks used for headless testing
