# ClientOverlay - Build & Setup Guide

## Prerequisites

- **Java 21** - Download from [Adoptium](https://adoptium.net/) or [Eclipse Temurin](https://www.eclipse.org/temurin/)
- **Gradle 8.10+** - Included via gradle wrapper (`./gradlew`)
- **Git** (optional, for cloning)
- **Minecraft Launcher** - With Fabric profile

## Step 1: Verify Java 21

```bash
java -version
```

Should output something like:
```
openjdk version "21.0.1" 2023-10-17 LTS
```

If not, set JAVA_HOME:
```bash
# Windows (Command Prompt)
set JAVA_HOME=C:\path\to\java\21

# Windows (PowerShell)
$env:JAVA_HOME="C:\path\to\java\21"

# Linux/Mac
export JAVA_HOME=/path/to/java/21
```

## Step 2: Build the Mod

Navigate to the ClientOverlay directory and run:

```bash
./gradlew build
```

Or on Windows:
```bash
gradlew.bat build
```

**First Build:** Will take 2-5 minutes as Gradle downloads:
- Fabric Loom
- Minecraft 1.21.1
- Yarn mappings
- Dependencies

**Subsequent Builds:** 30-60 seconds

### Expected Output

```
> Task :compileJava
> Task :processResources
> Task :classes
> Task :jar
> Task :sourcesJar
> Task :assemble
> Task :build

BUILD SUCCESSFUL in Xs
```

### Build Output Location

```
ClientOverlay/build/libs/clientoverlay-1.0.0.jar
```

## Step 3: Install Fabric (if not already installed)

1. Download [Fabric Installer](https://fabricmc.net/use/installer/)
2. Run it and select:
   - Minecraft Version: **1.21.1**
   - Create profile: **Yes**
   - Install: **Click Install**
3. Launch Minecraft with the new Fabric profile to generate `.minecraft/` directory

## Step 4: Install the Mod

1. Locate your `.minecraft/` folder:
   - **Windows:** `%APPDATA%\.minecraft`
   - **Linux:** `~/.minecraft`
   - **Mac:** `~/Library/Application Support/minecraft`

2. Create `mods/` folder if it doesn't exist

3. Copy `clientoverlay-1.0.0.jar` to `.minecraft/mods/`

## Step 5: Verify Installation

1. Launch Minecraft with Fabric profile
2. Wait for loading (first launch is slower)
3. Look for message in console: `[ClientOverlay] Initialized successfully!`
4. Press **Shift+Z** - should open config GUI
5. Press **F6** - should toggle detached camera

## Troubleshooting

### Build Fails: "Could not resolve net.fabricmc:yarn:1.21.1+build.9"

**Cause:** No internet connection or Fabric servers unreachable

**Solution:**
- Check internet connection
- Try again in a few minutes
- Use GitHub Codespaces/Replit if local internet unavailable

### Mod Doesn't Load

**Check console for errors:**
1. Launch Minecraft and wait for console
2. Look for `[ClientOverlay]` messages
3. Share full error message

**Common issues:**
- Wrong Fabric version - needs 0.16.x for 1.21.1
- Missing Fabric API - install from [CurseForge](https://www.curseforge.com/minecraft/mods/fabric-api)
- Wrong Java version - needs Java 21+

### Config GUI Won't Open

**Try:**
1. Verify Shift+Z works (might need to unmap in controls)
2. Check `.minecraft/config/clientoverlay.json` exists
3. Verify no errors in console

### Detached Camera Doesn't Move

**Try:**
1. Ensure F6 toggle worked (should see message if enabled)
2. Check WASD keys aren't mapped to GUI events
3. Verify mouse look is working

## Development Setup

### Setting Up IDE

**IntelliJ IDEA:**
1. Open project folder
2. Wait for Gradle sync
3. Right-click `src/` → Mark Directory as → Sources Root
4. Use IntelliJ's built-in Gradle runner

**Eclipse:**
1. File → Import → Gradle → Existing Gradle Project
2. Select ClientOverlay folder
3. Wait for sync

**VS Code:**
1. Install "Extension Pack for Java"
2. Install "Gradle for Java"
3. Open folder
4. Use VS Code's Gradle task runner

### Running in Development

IntelliJ:
1. Run → Edit Configurations
2. Click `+` → Gradle
3. Tasks: `runClient`
4. Run it

VS Code:
1. Press Ctrl+Shift+P
2. Type "Gradle: Run Task"
3. Select `runClient`

### Making Changes

1. Edit Java files in `src/main/java/`
2. Rebuild: `./gradlew build`
3. Restart Minecraft or use Fabric's hotload if available

### Debugging with Breakpoints

Most IDEs support Gradle debugger:
- Set breakpoint in your code
- Run in debug mode instead of run
- Game will pause at breakpoint

## Distribution

To share with others:

1. Build the mod: `./gradlew build`
2. Share file: `build/libs/clientoverlay-1.0.0.jar`
3. Include instructions:
   - Requires Java 21
   - Requires Fabric Loader 0.16.x
   - Requires Fabric API 0.102+
   - Place in `.minecraft/mods/`
   - Restart Minecraft

## Clean Build

If you want to start fresh:

```bash
./gradlew clean build
```

This removes all previously built artifacts and rebuilds from scratch.

## Advanced: Custom Configuration

You can modify `gradle.properties` to change:
- `minecraft_version` - Minecraft version to target
- `mod_version` - Your mod's version number
- `archives_base_name` - JAR filename prefix

For example, to target 1.21.2:
1. Change `minecraft_version=1.21.2`
2. Update `yarn_mappings=1.21.2+build.XFROM_MOJANG` (find latest on fabric.net)
3. Run `./gradlew clean build`

## Getting Help

If build fails:
1. Run with verbose output: `./gradlew build --debug`
2. Check error message - usually very specific
3. Search "[error message] fabric 1.21.1" on Google
4. Ask in [Fabric Discord](https://discord.gg/v6v4pMv) #mod-development

---

**Happy modding!**
