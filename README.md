# Reinforced Storage 26.2 Compatibility Port (Build 14)

Unofficial community compatibility port of Aton-Kish's MIT-licensed Reinforced Storage mods from Minecraft 1.21.11 to Minecraft 26.2 / Fabric.

This is a community port, not an official Aton-Kish release or endorsement. The Build 14
distribution targets Reinforced Barrels and Reinforced Chests. The `reinfshulker/` sources and
the original shulker jar under `legacy_reference/` remain in this source archive only as migration
reference material; the release build does not produce or advertise a shulker-box jar.

Upstream projects and attribution:

- [Aton-Kish/reinforced-barrels](https://github.com/Aton-Kish/reinforced-barrels)
- [Aton-Kish/reinforced-chests](https://github.com/Aton-Kish/reinforced-chests)
- [Aton-Kish/reinforced-core](https://github.com/Aton-Kish/reinforced-core)
- [Upstream 26.2 request](https://github.com/Aton-Kish/reinforced-chests/issues/112)

The original MIT license and copyright notice are retained in `LICENSE`. See
`docs/PUBLIC_RELEASE.md` for the community-release scope, installation order, testing status and
upstream handoff text.

## Primary requirement

Existing 1.21.11 worlds must load existing reinforced storage blocks/items without registry remapping or inventory loss.

This project intentionally prioritizes binary/world-data compatibility over new features or refactors.

## Baseline mods

- Reinforced Chests `4.0.0-beta+1.21.11`
- Reinforced Barrels `2.7.5+1.21.11`
- Reinforced Shulker Boxes `3.5.0-beta+1.21.11`

## Target

- Minecraft 26.2
- Fabric Loader 0.19.5
- Java 25 runtime/toolchain target
- Current compatible Fabric API for 26.2

## Build 14 Core configuration and GUI compatibility

Build 14 restores the upstream Reinforced Core configuration surface without changing registry IDs,
container sizes, slot order or saved data. With Mod Menu installed, open **Mods → Reinforced Core →
Configure** to select:

- `Single`, the Build 13 full-height storage layout; or
- `Scroll`, a viewport with a working scrollbar, mouse wheel and drag input.

The Scroll row count accepts the upstream range of 6–9 and defaults to 6. The configuration is
persisted as `config/reinfcore.json`. Cloth Config is embedded in each runtime jar through the
matching embedded Reinforced Core jar; Mod Menu remains an optional integration.

Build 14 keeps Build 13's validated migration behavior, placed-chest renderer and Nemo menu marker.
That renderer includes Build 12's complete-cell work and Build 13's wide-layout Nemo alignment:

- complete 18×18 cells are copied from the active `generic_54` chest sheet across every storage slot;
- both side frames extend to the last row, and the right frame moves to the actual right edge;
- header end sections are preserved, with repeated center strips for wider screens;
- the fixed 176×96 player panel stays centered and is copied intact once;
- unused space alongside the player panel remains unpainted;
- the automatic lower Nemo sorting row follows the centered player panel; and
- white labels and all existing menu/click/slot-order behavior remain unchanged.

Nemo's default lower buttons are initially anchored to the full container `imageWidth`. The client
screen moves only those four default lower widgets so they follow the centered player panel in both
Single and Scroll mode. The upper container buttons continue to follow the container edge. Explicit
Nemo x/y configuration remains authoritative.

The historical `atonkish.reinfcore.screen.ReinforcedStorageScreenHandler` marker remains in place
for Nemo's Inventory Sorting 26.2-1.21.3. The compatibility target is packs that preserve the standard
chest-sheet layout, including uniformly higher-resolution versions and transparent artwork.
No larger pack image or hand-authored Better-GUI compatibility image is required. Fixed illustrations,
nonstandard UV layouts and conditional GUI mods' matching rules are not automatically redesigned.
See `docs/BUILD12_RENDERER_FIX.md`, `docs/BUILD13_RENDERER_FIX.md` and
`docs/BUILD14_CONFIG_SCROLL_RESTORATION.md` for the exact contract and validation limits.

Run `bash tools/build_barrel_chest.sh` with Java 25 and Gradle to compile. The build now executes the
production Java screen and menu in recording harnesses. It checks cell coverage, frame placement,
intact player artwork, absence of shoulder filler, Nemo lower-row alignment, Scroll viewport slot
movement and scrollbar input. These are executable composition tests, not proof of a live
Minecraft/Fabric runtime.

The optional `compat_resource_pack/` bridge maps reinforced chest and barrel visuals to vanilla
texture IDs. Enable it when a dark/global resource pack should style these blocks; disable it to
return to the tier-colored reinforced skins. Put the dark/global pack above the bridge and the
reinforced retexture packs below it, then press F3+T. The bridge is a resource pack, not a jar, and
does not alter world data.

## Non-negotiable compatibility rules

1. Preserve original mod IDs and namespace identifiers unless 26.2 makes an identifier impossible.
2. Preserve block, item, block-entity, menu/screen-handler, recipe and tag IDs.
3. Preserve block state properties and meanings.
4. Preserve inventory slot counts per tier.
5. Preserve serialized inventory contents, stack counts, item components, custom names and lock/custom data.
6. Preserve shulker-box item-contained inventory data when placed, broken and upgraded.
7. Do not open the owner's canonical world until migration tests pass against disposable copies.
8. No gameplay or world-data feature additions during the compatibility port; client-only
   compatibility surfaces may be added when they do not alter saved data.

## Port order

1. Extract 1.21.11 binary/resource contract from the exact shipped jars.
2. Port shared Reinforced Core functionality.
3. Port Reinforced Barrels and validate save/load/hopper/comparator behaviour.
4. Port Reinforced Chests and validate single/double inventories.
5. Port Reinforced Shulker Boxes and validate item/block round trips and dye variants.
6. Build automated migration fixtures and compare inventories before/after.
7. Test on a copy of the owner's real world.
