# BlockMiner (Minecraft Forge 1.8.9)

**Author:** Curlgit 路 **Mod ID:** `blockminer` 路 **Version:** 1.0.0 路 **Minecraft:** Java Edition 1.8.9 路 **Forge:** 11.15.1.2318 路 **Java:** 8

Mine one block and BlockMiner removes more blocks of the **same type** for you, according to a per-block
"Extra Count". Every dimension (Overworld, Nether, The End) has its own independent configuration.

> **Status of this source:** the code was written against the Forge 1.8.9 API but has **not been compiled by the author**.
> A compiled JAR only exists after you run the Colab notebook successfully (`BlockMiner_Forge_1.8.9_Build.ipynb`).
> If the build fails, the notebook prints the Gradle log tail.

## Install

1. Build the mod with the Colab notebook (Run all) and download `BlockMiner-1.0.0.jar`.
2. Install Minecraft Forge 1.8.9 (11.15.1.2318 or newer 1.8.9 build).
3. Put the JAR into `.minecraft/mods/`.
4. Start the game.

## Commands

| Command | What it does |
|---|---|
| `/blockminer` or `/bm` | Opens the BlockMiner GUI |
| `/bm confirm` | Confirms a pending World Border task (also clickable in chat) |
| `/bm cancel` | Cancels your pending/running tasks |
| `/bm reload` | Reloads `config/blockminer.cfg` (owner / operators) |

## How mining works

* Defaults: Auto Mining **ON**, every block count **0**, World Border Mode **OFF**, particles **OFF**. With all counts at 0 nothing extra is ever mined.
* **Extra Count** = number of *additional* matching blocks (the block you mined is not counted). Range 0-99999. Click the number to type it directly (Enter = apply); `-` / `+` sit next to it.
* "Matching" = same block **and** same variant (stone vs granite, oak vs birch log, wool colours). Lit redstone ore counts as redstone ore.
* Search is a connected flood fill in 6 directions, starting with the direction you are looking at.
* The original block breaks normally. The extras are processed on the following server ticks (default 20 blocks / tick).
* Extras use real survival rules: correct drops (Fortune / Silk Touch), tool durability, hunger, XP, spawn protection, other mods' break-event vetoes.
  Drops are merged into few item stacks and appear at the block you mined.
* Safety: blocks that your tool cannot harvest, unbreakable blocks and tile-entity blocks (chests, furnaces...) are left alone. A job stops before your tool would break. Changing the held item cancels the job.
* Other dimensions (modded) are not affected.

## GUI

Title, three dimension tabs, search box, one scrollable block list (built from the block registry, no world scanning), `-` / count / `+`.
Click the count to type a number, or use `-` / `+`: click = 卤1, **Shift** = 卤10, **Ctrl** = 卤100, right-click on the count = reset to 0. Hover a name to see its registry id.
Top right: **Auto Mining** and **Particles** switches. Bottom: **WORLD BORDER MODE** for the selected tab, progress bar and Cancel button.
Changes are saved automatically.

## World Border Mode (per dimension, default OFF)

When ON for a dimension and you mine a block whose count is above 0, BlockMiner asks for confirmation in chat
(`[Confirm] [Cancel]`), then searches **all already loaded chunks** inside that dimension's world border for the same block type and removes them.

Limits and behaviour:

* **Never loads chunks.** Only chunks that are loaded right now are scanned, spiralling outward from the mined block up to `worldBorderChunkRadius` chunks (default 48). Blocks in unloaded areas are not touched.
* Incremental: max `checksPerTick` position checks (default 16384) and `blocksPerTick` removals (default 20) per tick. No freezing, no background scanning.
* Stops at `worldBorderMaxBlocks` removed blocks (default 2048) or `worldBorderMaxChecks` checks (default 20,000,000).
* In this mode the count only acts as the "selected" switch (> 0); the amount is limited by `worldBorderMaxBlocks`.
* Progress shows on a HUD bar and in the GUI; cancel with the Cancel button or `/bm cancel`.
* Independent for Overworld, Nether and The End.

## Confirm toggle

Next to WORLD BORDER MODE there is a **Confirm: ON/OFF** switch (default ON). OFF = no chat question, World Border tasks start immediately when you mine a selected block.

## DESTROYER MODE (prank / stress mode, always last in the GUI)

> **DISCLAIMER:** one click can remove up to **1,000,000 blocks**. This is extremely heavy: RAM/CPU overload, lag, and the game or phone **can hang or crash**. Use at your own risk, preferably on a throw-away world.

* Switch in the GUI (bottom, red). **Always OFF by default and it is never saved - it turns itself OFF after every restart.**
* When ON, mining **any** block removes that block type everywhere in the loaded chunks inside the world border (counts / selection are ignored), up to `limits.destroyerMaxBlocks` (default 1,000,000).
* It is still bounded per tick (`limits.destroyerBlocksPerTick`, default 100) and never loads chunks - only already loaded chunks are affected (radius `limits.destroyerChunkRadius`, default 128 chunks).
* No drops, no XP, no tool wear (so RAM is not flooded with item entities). Protected areas / other mods' break vetoes are still respected. Respects the Confirm switch.
* Cancel any time with `/bm cancel` or the Cancel button.

## Configuration: `config/blockminer.cfg`

Plain text, written automatically. Separate sections for `overworld`, `nether` and `end` (counts + `worldBorderMode`).
Only non-zero counts are stored, e.g. `overworld.count.minecraft\:stone=5`. Limits live under `limits.*`.
Settings persist across restarts. Edit the file only while the game is closed (or use `/bm reload`).

## Multiplayer

* The mod must be installed on the **server and on every client that wants the GUI** (same version).
* All mining is **server-authoritative**; the GUI only sends requests. Nothing is removed client-side.
* Anyone can open the GUI and view the settings; only the **world owner (single-player / LAN host)** or **server operators** can change them (others see a read-only GUI).
* The configuration is global per server (not per player).

## Build it yourself

Open `BlockMiner_Forge_1.8.9_Build.ipynb` in Google Colab and choose **Runtime 鈫� Run all**. It installs Java 8, downloads Gradle 2.14.1,
writes the project, builds with ForgeGradle 2.1, verifies the JAR and offers `BlockMiner-1.0.0.jar` and `BlockMiner-Source.zip` for download.
Manual build (Java 8 + Gradle 2.14.1): `gradle setupCIWorkspace build` 鈫� `build/libs/BlockMiner-1.0.0.jar`.

**[⬇ MOD LINK](https://github.com/Anshaks/BlockMiner/releases/tag/MOD)**
