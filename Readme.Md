# Project: Client-Server Game Architecture

This document provides a fully elaborated blueprint for a client-server
game architecture, detailing every component from the developer's
creative tools to the player's in-game experience. The system is
designed to be **cohesive, scalable, and secure**, with a focus on data
integrity and modular development.

------------------------------------------------------------------------

## 1. The Core Architecture

The game's foundation is a **client-server model** unified by a shared
set of **Game Datafiles**. This architecture separates the game's logic
(**Server**) from the player's interface (**Client**), ensuring a
consistent and secure experience.

-   **The Editor**: A comprehensive development tool that allows for
    world-building and live-editing of the game. It is the single point
    of creation for all static game content.
-   **The Client**: The player-facing application. It handles all visual
    rendering, player input, and communication with the server.
-   **The Server**: The game's central authority. It hosts the game
    world, processes all logic, and manages player data.
-   **Game Datafiles**: Encrypted files containing all the static
    information that defines the game world, from maps to item
    properties. They act as the "source of truth" and are synchronized
    between the Editor, Client, and Server.

------------------------------------------------------------------------

## 2. The Editor: The Creation & Live-Editing Tool

The Editor is a powerful application that serves as both the primary
development tool and a live-editing client. It provides a visual and
intuitive way to build the game world and its behaviors.

### A. Main Interface & Global Features

-   **Menu Bar**: Central hub for all major functions.
    -   **File**: Create, save, compile `.use` code, build, exit.
    -   **Artwork**: Import `.png` sprite/tileset assets.
    -   **Edit**: Cut, Copy, Paste tools for tiles, objects, chunks.
    -   **Game Data**: Access Map, Object, Animation editors.
    -   **Usecodes**: Manage object scripting.
    -   **Export**: Create standalone client/server or asset bundles.
    -   **View**: Toggle Toolbar, Status Bar, and UI elements.
-   **Toolbar**: Icons for frequent actions like Save, Cut, Copy, Paste,
    Zoom, Grid, and **Layer Mode**.
-   **Asset Filtering**: Dropdowns to filter by source (Original /
    Imported) and category (e.g., Weapons, Armor, NPCs).

### B. Specialized Editors

-   **Map Editor**
    -   Grid workspace, tile/object overlay, sprite palette.
    -   Terrain painting, object placement, **Chunk Mode** (4x4, 8x8,
        16x16).
-   **Object Type Editor**
    -   Assign name and family (e.g., Weapon, NPC).
    -   Configure Physical, Gameplay, and Visual properties.
    -   Link to sprite, animation, and usecode behavior.
-   **Animation Editor**
    -   Frame assembly for characters/objects.
    -   Set playback speed and looping.

### C. Core Development Workflow

1.  **Artwork Import**
2.  **Object Definition**
3.  **Animation Creation**
4.  **Map Design**
5.  **Scripting**
6.  **Export**

------------------------------------------------------------------------

## 3. The Usecode and Scripting System

The **usecode system** is an event-driven scripting layer enabling
object behavior without modifying the engine.

### A. The `.use` File Language

-   **Objects**: Define templates (e.g., `Object "Saw"`).
-   **Events**: Triggered actions (`OnUse`, `OnLook`).
-   **Commands**: Built-in actions (`Print`, `Create`, `TestSkill`).
-   **Properties**: Dynamic values (`player.health`, `object.quality`).

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

### B. API Calls & Editor Integration

-   `POST /api/object/use`: Define use behavior.
-   `GET /api/object/look`: Generate descriptions.
-   `POST /api/object/equip`: Set equip conditions.
-   `POST /api/event/run`: Handle OnDeath, etc.
-   `POST /api/teleport`: Handle movement.

------------------------------------------------------------------------

## 4. The Client: The Player's Interface

### A. Login System

-   **UI**: Account, password, login, registration (with email).
-   **Progress Bar**: Shows connection/sync status.
-   **Security**: SHA hash passwords.
-   **Safe Re-Log**: Auto sync + resume.
-   **Global Chat**: Event notifications.

### B. In-Game UI & Mechanics

-   **Chat System**: Toggled with `T`, expandable, scrollback log.

    -   In-View, Global, Guild (if applicable), Admin (if applicable).

-   **Guild System**

    -   **Purchase** to found guilds.
    -   Leader tools: Add (crosshair), Kick.
    -   Member view: List, Leave button.
    -   Guild Bank: Shared inventory.

-   **Game Area**: 16x16 tiles, centered on player, dynamic scrolling.

-   **Render & Stasis Zones**: Load 20-tile radius; stasis for
    off-screen monsters.

-   **Inventory & Equipment**

    -   Free-form backpack, movable items.
    -   Lock position via right-click.
    -   Gear slots: helmet, plate, shield, etc.

-   **Day/Night Cycle**: 1 real-hour = 1 game day.

-   **Skill Window**: Hotkey toggles view.

------------------------------------------------------------------------

## 5. The Server: The Game's Brain

### A. Core Server Functions

-   TCP hosting + game loop.
-   **Tick Speed**: Configurable.
-   **Movement**: Server-side validation.
-   **Data Management**: Player data, guilds, banks, usecode events.
-   **Export Options**: Standalone client/server, headless LAN server.

### B. Monster AI & Stasis

-   **Spawn Management**: Editor-defined.
-   **Active AI**: Enabled within 20 tiles.
-   **Stasis**: Disable AI beyond range.

### C. Player Death System

-   **Body Generation**: Drops items.
-   **Decay Timer**: 15 minutes, with stages.
-   **Final Decay**: Items drop to ground.
-   **Item Despawn**: 6-minute creeper process.

------------------------------------------------------------------------
