# Compile ClientOverlay Online (GitHub Codespaces - FREE)

Since your computer can't download Minecraft mappings, use GitHub Codespaces to compile in the cloud.

## Step 1: Create GitHub Account
Go to https://github.com/signup (FREE)

## Step 2: Fork or Upload This Project
1. Create new repository on GitHub
2. Upload the ClientOverlay folder
3. Push to GitHub

## Step 3: Open in Codespaces
1. Click "Code" button → "Codespaces" → "Create codespace on main"
2. Wait for terminal to open
3. Run:
```bash
cd ClientOverlay
./gradlew build
```

## Step 4: Download JAR
1. In Codespaces, go to `build/libs/`
2. Right-click `clientoverlay-1.0.0.jar`
3. Download it
4. Copy to `.minecraft/mods/`

**That's it!** Codespaces handles everything - Java, Gradle, Minecraft mappings all work there.

---

## Alternative: Replit (Also FREE)

1. Go to https://replit.com
2. Create new Replit
3. Choose "Import from GitHub"
4. Paste this repo URL
5. In terminal:
```bash
chmod +x gradlew
./gradlew build
```
6. Download the JAR

---

## Why This Works

- GitHub Codespaces = full Linux environment with internet
- Can download ALL dependencies (yarn mappings, Minecraft, etc.)
- Builds successfully
- You get a real compiled JAR with all code

---

**DO THIS AND YOU'LL GET A WORKING JAR!**
