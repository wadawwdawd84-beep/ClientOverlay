# ClientOverlay - Implementation Notes

## Architecture Overview

This mod is built on three core pillars:

### 1. Pure Client-Side
- **Never** modifies server state
- **Never** sends custom packets
- Uses only vanilla packets that the client already sends (player movement, camera angles)
- Safe for use on vanilla servers

### 2. Fabric API Events
- `ClientTickEvents.END_CLIENT_TICK` - Polls keybindings every tick
- `WorldRenderEvents.AFTER_TRANSLUCENT` - Renders debug overlays after terrain
- Events are the preferred Fabric method over Mixins when possible

### 3. Minimal Mixins
- Only 2 Mixins (Camera and ClientPlayerEntity)
- Both are necessary to intercept critical game logic
- Fully documented with comments

## Feature Details

### Feature 1: Spawner Wireframe Overlay

**Location:** `overlay/SpawnerWireframeRenderer.java`

**How it works:**
1. Iterates through all loaded chunks via `world.getChunkManager().getLoadedChunks()`
2. For each chunk, checks all block entities via `chunk.getBlockEntities().keySet()`
3. Filters for `MobSpawnerBlockEntity` instances
4. Checks distance to player with squared distance optimization
5. Draws wireframe using `RenderLayer.getLines()` + `VertexConsumer`
6. Optionally renders entity type label (placeholder for full text rendering)

**Rendering Pipeline:**
- Gets `VertexConsumerProvider.Immediate` from `Tessellator.getInstance().getBuffer()`
- Obtains line layer via `RenderLayer.getLines()`
- Draws 12 lines forming the box perimeter
- Uses `Matrix4f` from `WorldRenderContext` for proper positioning

**Performance:**
- Only runs during `WorldRenderEvents.AFTER_TRANSLUCENT`
- Iterates chunks once per frame (Minecraft loads/unloads dynamically)
- Squared distance check is faster than sqrt

### Feature 2: Entity Info Overlay

**Location:** `overlay/EntityInfoRenderer.java`

**How it works:**
1. Iterates all `PlayerEntity` instances in loaded world
2. Skips self in first-person view
3. Checks team membership via `Scoreboard.getPlayerTeam()`
4. Skips invisible players based on config
5. Projects player position to screen space (placeholder for full implementation)
6. Draws box + text overlay in world space

**Scoreboard Team Detection:**
```java
ScoreboardTeam team1 = scoreboard.getPlayerTeam(player1.getName().getString());
ScoreboardTeam team2 = scoreboard.getPlayerTeam(player2.getName().getString());
return team1 != null && team2 != null && team1.equals(team2);
```

**Screen-Space Projection:**
- Current implementation renders in world space
- Full implementation would use `Matrix4f` multiplication:
  ```java
  Vector4f screenPos = new Vector4f(worldX, worldY, worldZ, 1.0f);
  screenPos.mul(context.positionMatrix()); // Apply camera matrix
  screenPos.mul(context.projectionMatrix()); // Apply perspective
  // Now divide by w for screen coordinates
  ```

### Feature 3: Detached Camera

**Location:** `camera/DetachedCamera.java` + `mixin/CameraMixin.java`

**Architecture:**
1. `DetachedCamera` class stores camera position/rotation independently
2. `CameraMixin` injects into `Camera#update()` to override vanilla positioning
3. `ClientPlayerEntityMixin` freezes player movement via `tickMovement()`

**How It Works:**
1. On F6 press, `detachedCameraEnabled` flips true
2. Next camera update, Mixin detects this and calls `detachedCamera.savePlayerCamera()`
3. Player position is saved; camera starts at that position
4. Mixin sets camera.x/y/z to detached camera position
5. Each tick, `updatePosition()` reads WASD input:
   - Calculates forward/backward using camera yaw
   - Applies strafe perpendicular to yaw
   - Space/Shift move up/down
   - Ctrl multiplies speed by 3x
6. On F6 press again, calls `restorePlayerCamera()` and Mixin stops intercepting

**Math for Strafe:**
```java
float yawRad = Math.toRadians(cameraYaw);
float cosYaw = Math.cos(yawRad);
float sinYaw = Math.sin(yawRad);

// Forward relative to yaw
if (forward) {
    motionX += cosYaw * speed;
    motionZ += sinYaw * speed;
}

// Strafe left (perpendicular to forward)
if (left) {
    motionX += sinYaw * speed;  // 90° rotated
    motionZ -= cosYaw * speed;
}
```

**Mixin Injection Point:**
```java
@Inject(method = "update", at = @At("HEAD"), cancellable = true)
```
- Injects at HEAD so we can cancel the original vanilla update
- Uses `cancellable = true` to prevent default camera movement

## Configuration System

**File:** `.minecraft/config/clientoverlay.json`

**Loading:**
1. On mod init, `Config.load()` checks if file exists
2. If yes, uses Gson to deserialize JSON to Config object
3. If no, returns default Config with all settings initialized
4. Settings are applied immediately

**Saving:**
- Called after every GUI change
- Serializes Config object to JSON with pretty printing
- Preserves formatting for manual edits

**Safety:**
- Creates `.minecraft/config/` directory if it doesn't exist
- Silently falls back to defaults on parse error
- Never overwrites with partial data

## Event Lifecycle

```
Game Tick:
  ├─ ClientTickEvents.END_CLIENT_TICK
  │  ├─ Poll F6 for detached camera toggle
  │  ├─ Poll Shift+Z for overlay screen
  │  └─ Update detached camera position/rotation
  │
  └─ WorldRenderEvents.AFTER_TRANSLUCENT
     ├─ SpawnerWireframeRenderer.render()
     │  ├─ Iterate chunks
     │  ├─ Find MobSpawnerBlockEntity
     │  └─ Draw wireframes
     │
     └─ EntityInfoRenderer.render()
        ├─ Iterate players
        ├─ Project to screen space
        └─ Draw overlays
```

## Mixin Priority & Safety

**CameraMixin:**
- Target: `net.minecraft.client.render.Camera#update()`
- Method: HEAD injection with cancellation
- Safe because: We completely override with detached camera logic when enabled, otherwise do nothing

**ClientPlayerEntityMixin:**
- Target: `net.minecraft.client.network.ClientPlayerEntity#tickMovement()`
- Method: HEAD injection with cancellation
- Safe because: Only cancels when detached camera is active; otherwise passes through

## Performance Considerations

1. **Chunk Iteration:** Only happens once per render frame, Minecraft handles unloading
2. **Player Iteration:** Small list, linear scan is fine
3. **Distance Checks:** Use squared distances to avoid sqrt()
4. **Rendering:** Uses immediate-mode vertex submission, efficient for debug overlays
5. **Config Loading:** Only at startup, one JSON parse

## Limitations & Future Work

1. **Text Rendering:** Current implementation has placeholder for entity type text
   - Full implementation needs `TextRenderer` from context and screen-space projection
   - Would require proper font rendering + matrix stack management

2. **Screen-Space Projection:** Entity info overlay renders in world space
   - Full implementation would project to 2D screen coordinates
   - Requires computing screen position from world position + camera matrices

3. **Health Bar Rendering:** Not yet implemented
   - Would require quad rendering with precise positioning
   - Could use RenderLayer.getQuads() or direct quad rendering

4. **Config GUI Refinement:** Numeric input dialogs placeholder only
   - Could be enhanced with sliders for radius/speed settings

## Testing Checklist

- [ ] Spawner wireframes render correctly in all loaded chunks
- [ ] Entity info boxes appear above other players
- [ ] Detached camera F6 toggle works
- [ ] WASD movement works with correct strafe direction
- [ ] Ctrl multiplier works (3x speed)
- [ ] Config GUI opens on Shift+Z
- [ ] Config saves and loads correctly
- [ ] No custom packets sent (Wireshark/packet logger)
- [ ] Works on vanilla servers
- [ ] No console errors on load

## Code Quality

- Every method has clear comments
- Variable names are descriptive
- No magic numbers (constants defined with names)
- Proper null checks on all client instances
- Clean separation of concerns (Camera, Renderer, Config)
- Follows Minecraft/Fabric conventions
