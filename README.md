# Sky Crane 3D (`Podniebny Dźwig 3D`)

An interactive, single-file 3D WebGL web application built with **Three.js (`0.170.0`)** and **`astronomy-engine` (`2.1.19`)**, where giant strictly-perpendicular tower cranes transport a miniature 3D airplane across configurable real-world airport routes around a photorealistic Earth globe—without the airplane ever flying on its own.

All application code, styling, shaders, 3D models, physics, and UI live in a single self-contained file: [`index.html`](index.html) (Polish-language UI).

---

## What the Project Does

By default, the scene opens with a cinematic orbital fly-in onto Earth showing the default 3-stop route **Tokyo (`HND`) → Greenland (`SFJ`) → Paris (`CDG`)**:

1. **Crane 1** lowers twin steel hoist cables perpendicularly toward Tokyo's neon landing pad, attaches a 4-strap lifting harness to the airplane, hoists it above the clouds, swings smoothly over the Arctic, and sets it down on Greenland.
2. **Crane 2** slews over the Greenland ice pad, hooks the airplane, carries it across the North Atlantic, and gently lowers it beside the Eiffel Tower in Paris.
3. Upon touchdown at the destination, an HDR-blooming **3D fireworks show** launches around the landing pad before the cranes smoothly return for the next loop.

Users can freely orbit/zoom around Earth, jump to any stop, follow the airplane, scrub real-time UTC sunlight, explore the real-time astronomical sky, hover over celestial objects to identify them, or open the **Route Configurator** to build custom multi-stop routes between any real-world airports.

---

## Implemented Features

### 1. Photorealistic 3D Earth & Dual-Technique Volumetric Clouds
- **NASA Satellite Imagery**: Day (`earth-blue-marble.jpg`), night city lights (`earth-night.jpg`), lossless elevation topology relief (`earth-topology.png`) masked to land, and ocean specular reflections (`earth_specular_2048.jpg`).
- **Twilight & Atmosphere**: Smooth day/night terminator with orange-amber twilight glow, Fresnel horizon tint, and a Rayleigh-style additive atmospheric scattering halo (`BackSide` shader).
- **Planetary Shadow Casting**: Earth (`earthMesh.castShadow = true`) casts a real planetary umbra onto cranes and offshore rigs on the night side, paired with a dynamic camera-focused `4096×4096` (`2048×2048` on mobile) `PCFSoftShadowMap` frustum.
- **Dual-Technique Volumetric 3D Clouds**:
  - 10 concentric drifting cloud shells combining NASA cloud coverage (`earth_clouds_1024.png` alpha-masked) with 3D procedural noise and directional Beer-Lambert-style self-shadowing.
  - Instanced flat-bottomed 3D cumulus cloud clusters floating above oceans and continents.

### 2. Configurable Real-World Airport Routes (`✎ Trasa`)
- **Real-World Airport Database**: Loads OpenFlights `airports.dat` asynchronously (`7,697` real airports indexed cleanly with an instant built-in 25-airport fallback at boot).
- **Polish-Aware Instant Search**: Search by IATA/ICAO code, airport name, city, or country, including common Polish city exonyms (e.g. *Warszawa → Warsaw*, *Paryż → Paris*, *Nowy Jork → New York*, *Rzym → Rome*, *Monachium → Munich*).
- **Arbitrary Multi-Stop Routes (`N ≥ 2`)**: Add any number of stops, reorder (`↑` / `↓`), remove stops, validate minimum leg length (`≥ 160 km`) and maximum arc (`≤ 152°`), or pick one-click presets including **Around the World (6 stops: `WAW → DXB → HND → LAX → JFK → WAW`)**.
- **Dynamic Scene Rebuild (`rebuildRoute`)**: Reconstructs all landing pads, landmarks, cranes, route arcs, 120 Hz physics, declarative timeline, HUD pills, 3D clickable labels, camera presets, and destination fireworks cleanly while disposing prior geometries/materials.

### 3. Automatic Midpoint Cranes & 120 Hz Pendulum Physics
- **Right-Handed Perpendicular Geometry (`surfaceFrame`)**: Guarantees `det = +1` orthonormal bases (`east`, `up`, `south`) so every pad, tower crane, landmark, and rig stands strictly perpendicular to Earth's curved surface.
- **Exact Spherical Midpoint Placement**: Each of the `N − 1` cranes automatically mounts on an offshore hexagonal platform at the exact spherical midpoint between consecutive airports.
- **Perpendicular Twin-Rope Suspension**: Fixed radial trolley radius `(EARTH_RADIUS + H) * tan(theta)` keeps the twin hoist cables vertical relative to the Earth's surface at each endpoint.
- **Damped 120 Hz Pendulum Simulation**: Sub-stepped tangential pendulum physics (`stepPhysics`) responds realistically to crane angular acceleration and smoothly damps out sway when touching down, with seamless inductive airplane orientation handoffs across intermediate stops.
- **Regional Route Scaling & Anti-Collision Staggering**: On short regional routes (e.g., `WAW → MUC → ZRH`), pads, landmarks, offshore rigs, hook, ropes, airplane, fireworks, and camera framing scale proportionally (`routeScale`), while adjacent cranes whose booms share airspace automatically stagger mast heights vertically so jibs clear the neighboring crane's apex.
- **Physically Shaded Rig Bases & Stay Cables**: Offshore rig decks and accent bumpers (`createOffshoreRig`), crane stay cables (`stayMat`), and structures use physically shaded `MeshStandardMaterial` lighting so night-side bases darken naturally.

### 4. Landmarks & HDR 3D Destination Fireworks
- **Custom Landmarks**:
  - **Tokyo**: Cyberpunk multi-tower neon skyscraper complex with emissive window grids, HDR neon strips, billboard, holographic ring, and local neon light.
  - **Greenland**: Perpendicular faceted icebergs and ice landing pad.
  - **Paris**: Detailed 3D Eiffel Tower with tapered legs, decks, spire, and glowing beacon.
  - **All Other Airports**: Illuminated air-traffic-control tower with glowing observation cab, hangar apron, blinking aviation beacon, and rotating radar bar.
- **3D Destination Fireworks (`createDestinationFireworks`)**: GPU-buffered `THREE.Points` + `THREE.LineSegments` particle system anchored at the final airport featuring ascending trail rockets, burst flashes, dynamic `PointLight` illumination on nearby structures, radial gravity toward Earth, and four burst choreographies (Tricolore concentric shells, twinkling chrysanthemums, long-trailing golden willows, and tilted rings).

### 5. Real-Time Astronomical Sky & Mouse-Hover Identification
- **Live Ephemeris (`astronomy-engine`)**: Computes Greenwich Apparent Sidereal Time (`GAST`) and exact J2000 equatorial coordinates for live UTC (or any time selected via the **Sun UTC slider** / **"Teraz"** button).
- **Catalog-Accurate Celestial Sphere (`d3-celestial`)**:
  - **5,044 real Hipparcos stars** (`stars.6.json`) with B-V blackbody spectral colors, magnitude-calibrated sizing/luminance, subtle scintillation, and 2,200 Milky Way micro-stars.
  - **IAU J2000 Galactic Milky Way Dome**: Procedural FBM galactic plane with warm Sagittarius core, dark Great Rift dust lanes, and Large & Small Magellanic Clouds.
  - **Deep Sky Objects (`dsos.6.json`)**: 110 galaxies, nebulae, and clusters (including M31 Andromeda, M42 Orion, M45 Pleiades, Omega Centauri).
  - **Sun, Moon & Planets**: Corona-flared Sun, NASA-textured Moon (`moon_1024.jpg`) showing its exact live illuminated phase, and all 7 other Solar System planets (Mercury, Venus, Mars, Jupiter, tilted-ring Saturn, Uranus, Neptune).
- **Sky Map Toggle (`✦ Niebo`)**: Toggles 89 IAU constellation stick lines and Earth-occluded celestial labels.
- **Mouse-Hover Object Identification (`starnames.json`)**: Hovering the cursor anywhere over open sky displays a targeting reticle and tooltip with the object's name, Bayer/Flamsteed or Hipparcos designation, planet/lunar phase info, or magnitude—automatically occluded behind Earth's globe.

### 6. HDR Post-Processing, Depth Precision & Interactive Controls
- **Rendering Pipeline**: `HalfFloatType` `EffectComposer` + `RenderPass` + selective `UnrealBloomPass` (threshold `0.92`) + ACES `OutputPass`, procedural `PMREMGenerator` environment reflections, and `logarithmicDepthBuffer: true` with Three.js `logdepthbuf` chunks integrated into all custom shaders so back-side geometry never bleeds through Earth.
- **Interactive Controls**: `OrbitControls` (drag/zoom), Play/Pause, Restart, Speed (`1×` / `2×` / `0.5×`), Follow Airplane (`✈ Śledź`), Sky Map (`✦ Niebo`), Route Configurator (`✎ Trasa`), dynamic stop camera presets, clickable 3D labels, live UTC Sun slider + **"Teraz"** button, and an adaptive FPS guard for slower GPUs.

---

## Running Locally

Because ES module imports, NASA textures, and celestial/airport datasets are loaded over HTTPS and HTTP:

1. Serve `/root/project` with any static HTTP server, for example:
   ```bash
   python3 -m http.server 8080
   ```
2. Open `http://localhost:8080/index.html` in a modern WebGL2-capable browser with internet access (for `cdn.jsdelivr.net` assets).
