
Breakdown from this video : https://www.youtube.com/watch?v=K7__BjW4UWE
Generated from gemini pro in google ai studio

Here is a detailed, step-by-step tutorial based on the Polygon Runway video for creating a stylized 3D Isometric Ramen Shop in Blender.

---

# Tutorial: Creating a 3D Isometric Ramen Shop in Blender

This tutorial covers the complete workflow for modeling, texturing, lighting, and animating a stylized low-poly diorama.

**Prerequisites:**
*   **Blender 4.0+** installed.
*   Basic understanding of navigation (Orbit, Pan, Zoom).
*   **LoopTools Add-on enabled** (Edit > Preferences > Add-ons > Search "LoopTools" > Check box).

---

## Part 1: Setting Up the Base Scene

### 1. The Foundation
1.  **Cleanup:** Select the default Light and Camera. Press `X` to delete them. Keep the **Default Cube**.
2.  **Fix Origin:** Select the Cube. Press `Tab` for Edit Mode. Press `A` to select all. Press `G`, `Z`, `1`, `Enter`. This moves the mesh up 1 unit so the origin point sits at the bottom of the cube. Press `Tab` to exit Edit Mode.
3.  **Ground Plane:**
    *   Press `Shift + A` > Mesh > Plane.
    *   Press `S`, `4`, `Enter` to scale it up 4 times.
    *   **Beveling:** Press `Tab` (Edit Mode). Press `2` (Edge Select). Select the 4 corner edges (or `A` for all). Press `Ctrl + Shift + B` to bevel vertices. Scroll mouse wheel up to add segments for a rounded corner.
    *   **Thickness:** Press `A` to select all. Press `E` to extrude down slightly.

> **Pro Tip:** Go to the **Overlays** menu (top right of viewport) and check **Face Orientation**. If anything is Red, select the red faces in Edit Mode and press `Alt + N` > *Flip*. Turn off the overlay when done.

### 2. Shaping the Building
1.  Select the Cube. Press `S` to scale it to your desired building size.
2.  **Important:** Press `Ctrl + A` > **Apply Scale**. This ensures modifiers and bevels work correctly.
3.  **Bevel Edges:** In Edit Mode, select the vertical edges. Press `Ctrl + B` to bevel them. Scroll up to add segments (create a smooth, rounded corner).
4.  **The Shop Opening:**
    *   In Edit Mode, press `Ctrl + R` to add a horizontal loop cut. Slide it to the height of the counter.
    *   Add two vertical loop cuts to define the width of the shop window.
    *   Select the face where the opening should be. Press `E` to extrude inwards.
    *   Delete the face at the back of the extrusion and the bottom face of the extrusion to clean up geometry.

---

## Part 2: Modeling Architectural Details

### 1. Roof and Overhangs
1.  **Roof Rim:**
    *   Select the top face of the building. Press `I` to Inset.
    *   Press `E` to extrude down to create a hollow roof.
    *   **Rim Detail:** Select the top loop of faces (Alt + Click). Press `Shift + D` (Duplicate), `Right Click` (Cancel movement), then `P` > *Selection*. This separates it into a new object.
    *   Select the new roof object, enter Edit Mode, select all (`A`), and press `E` to extrude up slightly to create a cap.
2.  **Awning/Sign Holder:**
    *   Use loop cuts (`Ctrl + R`) on the front of the building to define a rectangular area above the opening.
    *   Select the face, `Shift + D` to duplicate, `P` to separate.
    *   Select the new object, extrude it outwards (`E`) to create a box shape.
    *   Use `Alt + E` > *Extrude Faces Along Normals* to add a border frame to this box.

### 2. The Curb/Platform
1.  Select the Ground Plane. Enter Edit Mode.
2.  Select the top face loop.
3.  `Shift + D`, then `P` > *Selection*.
4.  Select the new object. Press `A` to select all. Press `I` to Inset slightly.
5.  Delete the inner face.
6.  Select the remaining ring, press `E` to extrude up to create a sidewalk curb around the building.

### 3. The Counter
1.  Use the **3D Cursor** to place objects accurately. `Shift + Right Click` on the floor of the shop.
2.  `Shift + A` > Mesh > Plane.
3.  Scale and position it to act as the counter.
4.  Extrude it upwards (`E`) to give it thickness.
5.  Add a Bevel Modifier in the Modifiers tab for smooth edges.

---

## Part 3: Props and Assets

### 1. Stools
1.  `Shift + A` > Mesh > Circle (set vertices to 12 or 16).
2.  **Base:** Extrude up (`E`), Scale in (`S`), Extrude up (`E`).
3.  **Seat:** `Shift + D` the top circle, move up. Extrude up and Scale out to make a mushroom shape. Bevel the top edge (`Ctrl + B`).
4.  Add a **Subdivision Surface** modifier (`Ctrl + 2`) and Right Click > *Shade Smooth*.

### 2. The Giant Ramen Bowl (Roof Decoration)
1.  Place 3D cursor on the roof.
2.  Add a Circle. In Edit Mode, press `F` to fill.
3.  Extrude up (`E`), Scale out (`S`) to flare it like a bowl.
4.  Press `I` to inset the top face (rim thickness).
5.  Extrude down (`E`) into the bowl.
6.  Press `F` to create a face for the "soup" liquid.
7.  Add a **Solidify Modifier** for thickness and a **Subdivision Surface** modifier for smoothness.

### 3. Noodles (Procedural Curves)
1.  Add a Circle. Delete all vertices except two or three.
2.  Extrude vertices to draw a squiggle/noodle shape inside the bowl.
3.  Right Click > **Convert to Curve**.
4.  In the **Data Properties (Green Curve Icon)** > *Geometry* > *Bevel*, increase **Depth** to make the curve thick like a noodle.
5.  Duplicate (`Shift + D`) and rotate to fill the bowl.

### 4. Chopsticks & Egg
1.  **Chopsticks:** Create a Cylinder (8 vertices). Scale very thin and long. Rotate and place in the bowl.
2.  **Egg:** Add a Sphere, scale it. Cut it in half or scale on the Z-axis to flatten it slightly.

### 5. Cables and Pipes
1.  Select a wall face. `Shift + S` > *Cursor to Selected*.
2.  Add a Plane. Merge all vertices at center (`M` > *At Center*).
3.  Extrude the single vertex (`E`) to draw a cable line along the wall.
4.  Select the corner vertices. Press `Ctrl + Shift + B` to bevel the vertex (round the corners).
5.  Right Click > **Convert to Curve**.
6.  Add Depth in the Curve settings (as done with noodles).

### 6. Neon Text
1.  `Shift + A` > Text.
2.  Press `Tab` to edit. Type "RAMEN" (or paste Japanese Katakana: **ラーメン**).
3.  **Font:** Go to Data Properties (a icon). Under *Font*, load a font that supports thick characters or Japanese (e.g., Arial Unicode or a downloaded font).
4.  **Geometry:** Under *Geometry*, increase **Extrude** to give it 3D depth. Increase **Bevel Depth** slightly to catch light.
5.  Right Click > *Convert to Mesh*.

---

## Part 4: Materials and Shading

Go to the **Shading Workspace**.

1.  **Main Wall (Plaster):**
    *   Create a new material. Set Base Color to a soft off-white or beige. Increase Roughness (0.8).
2.  **Wood:**
    *   Create a material with a soft brown/orange color.
3.  **Metal:**
    *   Base Color: Dark Grey.
    *   **Metallic: 1.0**.
    *   Roughness: 0.3.
    *   **Distressed Look:** Connect a **Noise Texture** > **ColorRamp** > **Roughness** input. Adjust ColorRamp sliders to create shiny and rough patches.
4.  **Neon Lights:**
    *   Create a new material.
    *   Surface: Change *Principled BSDF* to **Emission**.
    *   Color: Red or Orange.
    *   Strength: Set to **15** or **20**.
5.  **Assigning Materials:** Select faces in Edit Mode, select the material in the list, and click **Assign**.

---

## Part 5: Lighting and Rendering (Cycles)

Switch Render Engine to **Cycles** (Properties Panel > Render Properties). Set Device to **GPU Compute**.

### 1. Volumetric Atmosphere (The "Vibe")
1.  `Shift + A` > Mesh > Cube. Scale it huge so it covers the entire scene.
2.  Create a new material for the cube named "Fog".
3.  Delete the *Principled BSDF*.
4.  `Shift + A` > Search for **Principled Volume**.
5.  Connect Volume to the **Volume** socket of the Material Output.
6.  Set **Density** very low: **0.05** or **0.1**.

### 2. Camera Setup
1.  `Shift + A` > Camera. Press `0` to look through it.
2.  In Camera Properties, switch Type to **Orthographic**.
3.  Adjust **Orthographic Scale** to zoom in/out.
4.  Position the camera at an isometric angle (Rotation X: 60, Z: 45 usually works well, or manually adjust).

### 3. Lights
1.  **Sun Light:** Add a Sun light. Rotate it to hit the side of the building. Strength: ~5. Angle: ~50 (for soft shadows).
2.  **Point Lights:** Add Point lights inside the shop (Warm color, Orange).
3.  **Spotlights:** Add a Spot light above the shop pointing down at the stool/counter area to create a "hero" pool of light through the fog.
4.  **Rim Light:** Add an Area Light behind the building, pointing toward the camera, usually Blue or cool-toned, to outline the silhouette.

---

## Part 6: Turntable Animation

1.  **The Pivot:** `Shift + A` > Empty > Plain Axes. Place it in the exact center of the scene.
2.  **Parenting:** Select the Camera and all lights/objects. Select the Empty **last**. Press `Ctrl + P` > *Object (Keep Transform)*.
3.  **Animation:**
    *   Select the Empty.
    *   Open the Timeline. Go to Frame 1.
    *   Press `I` > *Rotation*.
    *   Go to the last frame (e.g., 120).
    *   Press `R`, `Z`, `360`, `Enter` (rotate 360 degrees).
    *   Press `I` > *Rotation*.
4.  **Linear Interpolation:** By default, Blender eases in/out. To make it a constant loop:
    *   Select the keyframes in the timeline.
    *   Press `T` > **Linear**.

## Part 7: Final Output
1.  Go to **Output Properties**.
2.  Resolution: 1080x1080 (Square for Instagram) or 1920x1080.
3.  Frame Rate: 30 fps.
4.  Output Folder: Select your destination.
5.  File Format: **FFmpeg Video**.
6.  Encoding: Container > **MPEG-4**.
7.  Press **Ctrl + F12** to render animation.

---
*Tutorial adapted from Polygon Runway's workflow.*