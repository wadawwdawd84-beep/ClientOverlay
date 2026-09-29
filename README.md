# ClientOverlay - Minecraft Fabric Mod

A client-side utility mod for Minecraft 1.21.1 providing three debug/utility features:

## Features

### 1. Spawner Wireframe Overlay
- Automatically detects and renders wireframe boxes around all MobSpawnerBlockEntity instances
- Configurable detection radius (default 64 blocks)
- Optional entity type label rendering
- Uses `WorldRenderEvents.AFTER_TRANSLUCENT` for reliable rendering

### 2. Entity Info Overlay
- Displays information about nearby players (name, distance, health)
- Projects player bounding boxes to screen space
- Teammate detection via Scoreboard
- Option to skip invisible players
- Configurable display options

### 3. Detached Camera (Spectator View)
- Toggle with F6 keybind
- Independent camera movement using WASD + Space/Shift
- 3x speed multiplier with Left Ctrl
- Player stays in place while camera moves
- Automatically restores camera on disable

## Installation

1. Ensure you have Fabric Loader 0.16.x installed for Minecraft 1.21.1
2. Ensure you have Fabric API 0.102.x installed
3. Place `clientoverlay-1.0.0.jar` in your `.minecraft/mods/` folder
4. Launch Minecraft with the Fabric profile

## Configuration

Configuration is stored in `.minecraft/config/clientoverlay.json`

```json
{
  "spawnerWireframeEnabled": true,
  "spawnerWireframeRadius": 64,
  "spawnerShowEntityType": true,
  "entityInfoEnabled": true,
  "entityInfoShowHealth": true,
  "entityInfoSkipTeammates": true,
  "entityInfoSkipInvisible": true,
  "detachedCameraEnabled": false,
  "detachedCameraKeybind": 65,
  "detachedCameraBaseSpeed": 0.1,
  "detachedCameraSpeedMultiplier": 3.0
}
```

## Controls

- **Shift + Z**: Open configuration GUI
- **F6**: Toggle detached camera
- **WASD**: Move camera (when detached)
- **Space**: Move up (when detached)
- **Shift**: Move down (when detached)
- **Ctrl**: 3x speed multiplier (when detached)

## Building

```bash
./gradlew build
```

The JAR will be built to `build/libs/clientoverlay-1.0.0.jar`

## License

MIT

## Notes

- This mod is **completely client-side** and sends no custom packets
- All rendering uses vanilla Fabric API events
- No impact on server gameplay or detection
- Works on any server that allows Fabric mods
