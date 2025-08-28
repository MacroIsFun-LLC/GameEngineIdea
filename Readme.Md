# Project: Client-Server Game Architecture

This document provides a fully elaborated blueprint for a client-server
game architecture, detailing every component from the developer's
creative tools to the player's in-game experience. The system is
designed to be **cohesive, scalable, and secure**, with a focus on data
integrity and modular development.

------------------------------------------------------------------------

## 1. The Core Architecture

The game's foundation is a **client-server model** unified by a shared
set of **Game Datafiles**.\
This architecture separates the game's logic (**Server**) from the
player's interface (**Client**), ensuring a consistent and secure
experience.

-   **The Editor**: A comprehensive development tool for world-building
    and live-editing. It is the single point of creation for all static
    game content.\
-   **The Client**: The player-facing application. Handles rendering,
    input, and server communication.\
-   **The Server**: The game's central authority. Hosts the game world,
    processes logic, and manages player data.\
-   **Game Datafiles**: Encrypted files containing all static game
    definitions (maps, items, etc.). They serve as the **source of
    truth** across Editor, Client, and Server.

------------------------------------------------------------------------

## 2. The Editor: The Creation & Live-Editing Tool

The Editor is both the **primary development tool** and a **live-editing
client**, providing a visual and intuitive way to build the game world.

### A. Main Interface & Global Features

-   **Menu Bar**
    -   **File**: Create, save, compile `.use` code, build, and exit.\
    -   **Artwork**: Import `.png` files for sprites/tilesets.\
    -   **Edit**: Cut, Copy, Paste for tiles, objects, and chunks.\
    -   **Game Data**: Access specialized editors (Map, Object,
        Animation, etc.).\
    -   **Usecodes**: Manage scripts for objects.\
    -   **Export**: Package standalone client, headless server, or
        specialized files.\
    -   **View**: Toggle Toolbar, Status Bar, and UI components.
-   **Toolbar**: One-click access to save, copy, paste, navigation
    (Zoom, Grid), and **Layer Mode** (terrain, objects, tiles).\
-   **Asset Filtering**: Dropdowns to filter assets by **source**
    (Original / Imported) and **category** (Weapons, Armor, NPCs, etc.).

### B. Specialized Editors

-   **Map Editor**
    -   Grid workspace + overlay grid.\
    -   Palette of sprites/assets.\
    -   Terrain painting, object placement, and **Chunk Mode**
        (duplicate large sections like 4×4, 8×8, 16×16).
-   **Object Type Editor**
    -   Assign **name** + **family** (Weapon, Armor, NPC).\
    -   Properties: Physical (Weight, Mass), Gameplay (Collectible,
        Container), Visual (Illumination, Brightness).\
    -   **Assignment**: Link to sprite + animation. Includes **Edit
        Usecode** button.
-   **Animation Editor**
    -   Assemble sprite frames into animations.\
    -   Control playback speed and sequencing.

### C. Core Development Workflow

1.  **Artwork Import** -- Import all `.png` assets.\
2.  **Object Definition** -- Define objects, properties, and assign
    sprites.\
3.  **Animation Creation** -- Build character/object animations.\
4.  **Map Design** -- Build world maps with objects, filtering, and
    chunks.\
5.  **Scripting** -- Add behaviors via Usecodes.\
6.  **Export** -- Compile into final obfuscated game files.

------------------------------------------------------------------------

## 3. The Usecode and Scripting System

The **usecode system** is the backbone of object-based interactions. It
is an **event-driven scripting language** that allows high customization
without engine modification.

### A. The `.use` File Language

**Core Syntax:** - **Objects**: Named templates for items/characters.\
- **Events**: Triggered blocks (e.g., `OnUse`, `OnLook`).\
- **Commands**: Built-in actions (`Print`, `Create`, `TestSkill`).\
- **Variables/Properties**: Example: `player.health`, `object.quality`.

### Example Structure

``` text
Object "Saw" {
    OnUse {
        Print "You cut through the log.";
    }
    OnLook {
        Print "A sturdy saw with a wooden handle.";
    }
}
```

### B. API Calls & The Editor's Role

The Editor translates visual inputs → `.use` code through backend API
calls:

-   `POST /api/object/use` → Define primary function (e.g., heal
    player).\
-   `GET /api/object/look` → Define descriptions (e.g., durability
    states).\
-   `POST /api/object/equip` → Requirements for equipping (stats,
    skills).\
-   `POST /api/event/run` → Handle events (e.g., OnDeath spawn loot).\
-   `POST /api/teleport` → Handle movement/teleports.

------------------------------------------------------------------------

## 4. The Client: The Player's Interface

The **Client** is the secure entry point for players.

### A. Login System

-   **UI Elements**: Account, Password, Login button, Create Account
    form (with email).\
-   **Progress Bar**: Displays connection + file sync progress.\
-   **Security**: Passwords are SHA hashed before server transmission.\
-   **Safe Re-Log**: Progress saved, checksum ensures updated files.\
-   **Global Chat**: Displays system + in-game event messages.

### B. Player & Stat UI

-   **Player Name** -- Displayed prominently.\
-   **Health** -- Vital status bar.\
-   **Stats** -- Strength, Construction, Dexterity, Intelligence.\
-   **Stat Point Allocation** -- Allocate starter points via button.

------------------------------------------------------------------------

## 5. The Server: The Game's Brain

The **Server** is the logical core, running independently of the Editor.

-   **Hosting**: TCP socket + continuous **Game Loop**.\
-   **Data Management**: Load/save player data, run usecodes, process
    world events.\
-   **Export Options**: Editor can produce:
    -   Standalone **client executable**.\
    -   **Headless server executable** (for LAN or single-player).

------------------------------------------------------------------------
