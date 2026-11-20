# Custom 3D Line-Drawn Android Game Engine — Master Instructions

## Project Vision
This is a native Android 3D sandbox game engine where **everything is constructed and simulated using 3D lines**. Users create projects from a Project Hub, start in an empty 3D editor viewport, draw shapes (cars, wheels, doors, buildings) and natural terrain out of lines, attach modular physics & behavior logic to those shapes, and test/play their creations in **First-Person 3D mode**.

## Tech Stack Rules
- **Language:** Kotlin (minSdk 26, targetSdk 35).
- **UI Layer:** Jetpack Compose + Material 3 (Dark Studio Theme) layered over a `GLSurfaceView`.
- **Graphics:** OpenGL ES 3.0. All visuals use custom line shaders (thick screen-space quads + optional depth-only occlusion meshes so foreground shapes hide background lines unless X-Ray is enabled).
- **Physics:** `com.github.stephengold:jolt-jni-Android` for RigidBodies, ConvexHulls, HeightField terrain, `VehicleConstraint`, `HingeConstraint`, and `CharacterVirtual` (First-Person controller).
- **Math & Data:** Pure Kotlin math (`Vec3`, `Quat`, `Mat4`) + `kotlinx.serialization` for JSON project persistence.

## Core Architectural Principles
1. **Strict Editor / Runtime Separation:**
   - The `SceneDocument` (serializable data) is the single source of truth in Editor Mode.
   - Pressing **Play** clones `SceneDocument` into a live `PhysicsWorld` + `RuntimeState`.
   - Pressing **Stop** tears down `PhysicsWorld` and restores the untouched `SceneDocument`.
2. **Thread Safety:**
   - Compose runs on the Main UI thread; OpenGL rendering & physics stepping run on the `GLSurfaceView` GLThread.
   - Communicate via thread-safe command queues (`ConcurrentLinkedQueue<EngineCommand>`) and immutable UI state snapshots (`StateFlow`).
3. **Everything Is Line-Drawn:**
   - Never rely on external `.obj`/`.fbx`/textured PBR assets.
   - Every entity renders via `LineMesh` (polylines + optional depth-occlusion triangle indices).
4. **Coordinate System:**
   - Right-handed Y-up (`+X` Right, `+Y` Up, `+Z` Forward/Toward Viewer). 1 unit = 1 meter.
