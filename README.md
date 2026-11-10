# iso-nyc — East River Diorama

A photoreal, real-time Three.js scene of the Manhattan skyline seen from the Brooklyn waterfront, with the Brooklyn Bridge, a promenade, DUMBO lofts, and a cut-away subway station underneath. Everything is modeled in meters and shot with a telephoto, shifted-lens camera 300 m back, so scale and haze behave like a photograph.

![Golden hour](renders/golden-hour.png)

## Controls

- **Time of day** – a 24-hour scrubber (track painted with the actual sky colors), quick jumps for dawn / morning / noon / golden hour / dusk / night, and play/pause. Sunrise and sunset use the real late-October NYC times.
- **Weather** – clear, clouds, rain, snow, fog, thunderstorm. Conditions blend into each other; snow builds up over time on roofs, cars and curbs and melts away again.
- Drag to pan along the river on narrow screens; mouse movement adds a little parallax on desktop.

## What's in the scene

- Scene-linear HDR pipeline: half-float render targets, bloom fed by real highlight energy, metered auto exposure that adapts over about a second, and an AgX tone curve with light chromatic aberration, vignette and grain.
- Atmosphere model: Rayleigh, Mie and ozone single scattering on a spherical earth plus a multiple-scattering fill, evaluated on the CPU into small lookup textures. It drives the sky, the sun color, ambient light and reflections, so noon is blue, golden hour glares, and dusk goes purple over orange. Stars, moon, city glow and cloud layers sit on top.
- Directional aerial perspective: haze has height falloff and takes the color of the sky behind it, so the skyline dissolves into the horizon and glows toward the sun.
- Analytic local lights (street lamps, platform fluorescents, tunnel lamps) computed in the material shaders instead of point lights; the underground is lit only by its own fixtures.
- Planar-reflected river with wave distortion, sun glitter and rain rings.
- Procedural facades (curtain wall, limestone, prewar masonry, brick lofts) with parallax-occluded window reveals, blinds and curtains, and per-building window lighting at night.
- Landmarks: Empire State (orange for Halloween week), Chrysler crown, One World Trade Center, the Brooklyn Bridge with its cable web, and a DUMBO clock tower.
- Street life: taxis, sedans, an NV200 cab and an MTA bus; pedestrians with umbrellas in rain; Bishop's-crook lamps; benches, hydrants, crosswalks, a bus shelter and a Con Ed steam stack; autumn trees with dense leaf clusters.
- Subway cut-away: five tiled platforms lit only by their fluorescent fixtures, a stainless-steel train that runs every 10–20 s, a track pit with ballast and rails, and a geological section through asphalt, concrete and earth.
- River and air traffic: NYC Ferry, water taxi, tug and barge, a jet with nav strobes, a helicopter, bird flocks.

## Renders

| Morning | Night |
| --- | --- |
| ![Morning](renders/morning.png) | ![Night](renders/night.png) |

| Rain | Fog | Snow |
| --- | --- | --- |
| ![Rain](renders/rain.png) | ![Fog](renders/fog.png) | ![Snow](renders/snow.png) |

## Running it

It is a single `index.html`. Open it directly, or serve the folder with any static server:

```sh
npx serve .
```

Dependencies (three r128 plus its EffectComposer, UnrealBloomPass and Reflector helpers) load from CDN.

## Notes

- Performance: everything static is baked to world space and merged per material at load (about 1,570 meshes into 162), the river reflection skips whatever cannot appear in it, and a frame is roughly 600 draw calls instead of 7,000. If a device cannot hold about 45 fps, a governor steps quality down once (resolution, then reflection and shadow cost). `window.__perf()` in the console reports draw calls and triangles per pass.
- Still a heavy scene for fill rate: HDR bloom, planar reflection, soft shadows and parallax mapping. The control panel collapses with the arrow button so the subway is visible on narrow screens.
- Built with Three.js r128. Nothing to compile.
