# ModelProxy

### Interactive 3D-like views without exposing the 3D model.

**ModelProxy** is a 2D render-proxy system for sharing interactive views of 3D artwork on the web without delivering the original 3D asset to the viewer.

Most web-based 3D portfolios and product viewers work by sending a web-compatible representation of the 3D model to the client's browser.

That creates an important problem:

> **If the browser can render the 3D model, the model is present on the client side.**

With enough technical knowledge and the right browser tools, client-side 3D assets can potentially be inspected, extracted, or reconstructed.

ModelProxy takes a different approach.

Instead of sending the 3D model to the browser, it renders the model into a **matrix of 2D images** and uses those images as an interactive proxy.

```text
                    ARTIST
                      │
                      │ Original 3D Model
                      │
                      ▼
              ┌─────────────────┐
              │   ModelProxy    │
              │                 │
              │  3D → 2D views  │
              └────────┬────────┘
                       │
                 Render Matrix
                       │
                       ▼
             ┌───────────────────┐
             │ Interactive 2D    │
             │ Render Proxy      │
             └─────────┬─────────┘
                       │
                       ▼
                    CLIENT
```

The client receives rendered images — **not the original 3D geometry**.

---

## The problem

A 3D artist may want to show a client:

> "Here is the model. Rotate it and inspect it."

The conventional solution is to create a web viewer.

The viewer loads a web-compatible 3D asset such as:

* GLB
* glTF
* OBJ
* FBX converted to a web format
* or another browser-renderable representation

But this means the browser needs access to the asset.

Once the asset reaches the client, it is no longer entirely under the artist's control.

A technically capable user can inspect browser resources, network requests, JavaScript objects, WebGL resources, downloaded assets, textures, or other client-side data.

Some platforms address this with sophisticated protection and encryption mechanisms.

ModelProxy explores a simpler architectural alternative:

### Don't send the 3D model.

---

# The ModelProxy approach

ModelProxy converts the 3D model into a **render matrix**.

For example:

```text
              YAW

        0   1   2   3   4   5   6   7

P   0   ●   ●   ●   ●   ●   ●   ●   ●
I   1   ●   ●   ●   ●   ●   ●   ●   ●
T   2   ●   ●   ●   ●   ●   ●   ●   ●
C   3   ●   ●   ●   ●   ●   ●   ●   ●
H   4   ●   ●   ●   ●   ●   ●   ●   ●
    5   ●   ●   ●   ●   ●   ●   ●   ●
    6   ●   ●   ●   ●   ●   ●   ●   ●
    7   ●   ●   ●   ●   ●   ●   ●   ●
```

Each point represents a rendered 2D image from a particular orientation.

An 8 × 8 matrix produces **64 rendered views**.

The viewer then switches between those images according to user interaction.

### Horizontal movement

Changes the yaw.

### Vertical movement

Changes the pitch.

The result feels like interacting with a 3D object, but the browser is actually displaying a sequence of 2D renders.

---

# 3D → 2D → Interactive

The fundamental transformation is:

```text
             Original 3D Asset
                     │
                     ▼
              3D Rendering
                     │
          ┌──────────┴──────────┐
          │                     │
          ▼                     ▼
       Pitch 0               Pitch N
       ┌─────┐               ┌─────┐
       │ PNG │ ...           │ PNG │
       └─────┘               └─────┘
          │                     │
          └──────────┬──────────┘
                     ▼
                Render Matrix
                     │
                     ▼
             Interactive Proxy
```

After rendering, the client-side viewer only needs the resulting 2D representation.

## The prototype renders the GLB off-screen, captures transparent PNG frames, and then discards the 3D rendering scene.

# Why call it a "Proxy"?

The rendered images act as a **visual proxy for the original model**.

The proxy preserves:

* visual appearance
* approximate viewpoint interaction
* transparency
* pitch/yaw inspection
* presentation quality

But does not need to preserve:

* mesh topology
* vertices
* faces
* UVs
* scene hierarchy
* original materials
* original textures
* editable geometry

The viewer is therefore interacting with a representation of the model rather than the model itself.

---

# Not a security claim

ModelProxy is **not DRM**.

It does not make the visual content impossible to copy.

A user can still:

* take screenshots
* record the screen
* save the rendered images
* potentially reconstruct approximate geometry from enough views

The objective is narrower:

> **Avoid exposing the original 3D asset to the client browser simply because the client needs an interactive view.**

This makes ModelProxy an **IP exposure reduction technique**, rather than a claim of perfect content protection.

---

# Current prototype

The current prototype supports:

* GLB input
* transparent rendering
* configurable render matrices
* 4 × 4 rendering
* 8 × 8 rendering
* 12 × 12 rendering
* 30 × 30 rendering
* 256 px rendering
* 512 px rendering
* 1024 px rendering
* multiple lighting presets
* mouse interaction
* touch interaction
* frame matrix inspection
* standalone HTML export
* ZIP export

The current implementation provides 4×4 through 30×30 matrix options.

---

# Export

## Standalone HTML

ModelProxy can package the render matrix into a single HTML file.

The resulting viewer contains the rendered frames and interaction logic without requiring the original 3D model.

This makes it possible to:

* host the viewer
* send it to a client
* embed it in another website
* use it as a portfolio component
* archive a presentation

The prototype embeds the rendered frame data directly into the generated HTML.

## ZIP Bundle

Alternatively, ModelProxy can generate:

```text
modelproxy/
├── index.html
└── frames/
    ├── frame_0_0.png
    ├── frame_0_1.png
    ├── frame_0_2.png
    └── ...
```

The ZIP contains the viewer and its 2D render frames.

---

# Potential applications

### 3D artists

Show clients interactive work without distributing the source asset.

### Freelancers

Send interactive previews during review and approval.

### Design studios

Share work with clients while keeping production files private.

### Product visualization

Provide interactive product inspection without delivering production geometry.

### Architecture

Share interactive visualizations without exposing the underlying scene.

### Game art

Show characters, props and environments without distributing production assets.

### Portfolios

Create interactive 3D-like portfolio pieces without requiring visitors to download 3D assets.

---

# Technology

Current prototype:

* JavaScript
* Three.js
* GLTFLoader
* WebGL
* HTML/CSS
* JSZip

The rendering engine uses an off-screen WebGL renderer with transparency and captures each orientation as a PNG.

---

# Roadmap

* [x] GLB input
* [x] 2D render matrix
* [x] Transparent PNG rendering
* [x] Interactive pitch/yaw navigation
* [x] Mouse interaction
* [x] Touch interaction
* [x] Frame inspector
* [x] Standalone HTML export
* [x] ZIP export

### Planned

* [ ] More 3D input formats
* [ ] Custom pitch/yaw ranges
* [ ] Adaptive frame density
* [ ] Better interpolation between frames
* [ ] Background controls
* [ ] Render/material presets
* [ ] Image compression
* [ ] Web hosting
* [ ] Private project storage
* [ ] Shareable URLs
* [ ] Expiring links
* [ ] Access controls
* [ ] Clie
