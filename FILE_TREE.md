# ClientOverlay - Complete File Structure

```
ClientOverlay/
├── fabric.mod.json
├── clientoverlay.mixins.json
├── build.gradle
├── gradle.properties
├── settings.gradle
├── gradle/
│   └── wrapper/
│       └── gradle-wrapper.properties
├── src/
│   └── main/
│       ├── java/
│       │   └── com/
│       │       └── clientoverlay/
│       │           ├── ClientOverlayClient.java (Main initializer)
│       │           ├── Config.java (JSON config manager)
│       │           ├── camera/
│       │           │   └── DetachedCamera.java (Camera state & movement)
│       │           ├── mixin/
│       │           │   ├── CameraMixin.java (Injects into Camera#update)
│       │           │   └── ClientPlayerEntityMixin.java (Injects into ClientPlayerEntity#tickMovement)
│       │           └── overlay/
│       │               ├── SpawnerWireframeRenderer.java (Renders spawner boxes)
│       │               ├── EntityInfoRenderer.java (Renders player info)
│       │               └── OverlayScreen.java (Config GUI)
│       └── resources/
│           └── (fabric.mod.json & mixins JSON already listed above)
└── README.md
```

## File Descriptions

**Root Config Files:**
- `fabric.mod.json` - Mod metadata & entry points
- `clientoverlay.mixins.json` - Mixin configuration
- `build.gradle` - Gradle build configuration
- `gradle.properties` - Project properties (versions, artifact name)
- `settings.gradle` - Gradle settings
- `gradle/wrapper/gradle-wrapper.properties` - Gradle wrapper version

**Main Code:**
- `ClientOverlayClient.java` - ClientModInitializer, registers all events and keybindings
- `Config.java` - Loads/saves JSON config from ~/.minecraft/config/clientoverlay.json

**Camera System:**
- `DetachedCamera.java` - Manages detached camera state, position, rotation, and movement

**Mixins:**
- `CameraMixin.java` - @Mixin(Camera.class), overrides update() to implement detached camera
- `ClientPlayerEntityMixin.java` - @Mixin(ClientPlayerEntity.class), freezes player movement when camera is detached

**Overlay Renderers:**
- `SpawnerWireframeRenderer.java` - Uses WorldRenderEvents.AFTER_TRANSLUCENT to draw wireframe boxes around spawners
- `EntityInfoRenderer.java` - Renders player names, distances, health bars above other players
- `OverlayScreen.java` - Screen GUI with toggle buttons for all features

**Documentation:**
- `README.md` - Installation, configuration, and usage guide
- `FILE_TREE.md` - This file
