# Wolf Armor Slot — Fabric Mod for Minecraft 1.21.11 (Mounts of Mayhem)

Adds a dedicated **Wolf Armor** trinket slot to the player inventory.  
While wolf armor occupies the slot, **the player is immortal** — every hit drains the armor's
durability instead of the player's health, exactly like wolves wearing wolf armor in vanilla.
When the armor breaks, the player becomes mortal again.

---

## How it works

| Situation | Behaviour |
|---|---|
| Wolf armor equipped, has durability | All incoming damage (any source) is absorbed. Durability decreases by `round(damage)`, min 1. Player HP unchanged. |
| Wolf armor equipped, **durability reaches 0** | Item breaks (normal break sound + particles). Player is mortal again from this point. |
| No wolf armor in slot | Normal vanilla damage. |

This mirrors exactly how tamed wolves survive hits — they are unkillable while the armor holds.

---

## Requirements

| Dependency | Version |
|---|---|
| Minecraft (Java) | **1.21.11** (Mounts of Mayhem) |
| Fabric Loader | ≥ 0.18.1 |
| Fabric API | 0.119.5+1.21.11 |
| [Trinkets Updated](https://modrinth.com/mod/trinkets-updated) | 3.11.0-beta.4+1.21.11 |

> ⚠️ This mod targets **1.21.11 specifically**. It will not work on 1.21.1 or earlier without
> changes to the build script and mappings.

> ⚠️ The original **Trinkets** mod only goes up to 1.21.1. For 1.21.11 you need
> **[Trinkets Updated](https://modrinth.com/mod/trinkets-updated)** (by Patbox).

---

## Building

```bash
# Requires Java 21+
./gradlew build
```

Output jar: `build/libs/wolf-armor-slot-1.0.0.jar`

> **Note on mappings**: 1.21.11 is the last version to support Yarn mappings, but Fabric recommends
> switching to **Mojang's official mappings** for forward-compatibility. This project uses
> `loom.officialMojangMappings()` accordingly.

---

## Installation

1. Install [Fabric Loader 0.18.1+](https://fabricmc.net/use/)
2. Drop into `mods/`:
   - [Fabric API](https://modrinth.com/mod/fabric-api) (`0.119.5+1.21.11`)
   - [Trinkets Updated](https://modrinth.com/mod/trinkets-updated) (`3.11.0-beta.4+1.21.11`)
   - `wolf-armor-slot-1.0.0.jar`

---

## Customisation

### Change how much durability each hit drains
In `LivingEntityMixin.java`, edit this line:
```java
int durabilityDrain = Math.max(1, Math.round(amount));
```
For example, `Math.max(1, (int)(amount * 2))` makes armor break twice as fast.

### Make certain damage types bypass the armor (e.g. void/fall-into-void)
```java
// In LivingEntityMixin, add before the immortality block:
if (source.is(net.minecraft.tags.DamageTypeTags.BYPASSES_INVULNERABILITY)) return;
```

### Add a visual renderer (wolf armor on player model)
Implement `TrinketRenderer` and register it in `WolfArmorSlotClient`:
```java
TrinketRendererRegistry.registerRenderer(Items.WOLF_ARMOR, new MyWolfArmorRenderer());
```

---

## Project structure

```
src/main/java/com/wolfarmor/
├── WolfArmorSlotMod.java          — registers trinket, mod init
├── WolfArmorTrinket.java          — slot policy + static helpers
├── client/
│   └── WolfArmorSlotClient.java   — client init
└── mixin/
    └── LivingEntityMixin.java     — ★ core immortality hook

src/main/resources/
├── fabric.mod.json
├── wolfarmor.mixins.json
├── assets/wolfarmor/lang/en_us.json
└── data/wolfarmor/
    ├── trinkets.json               — registers wolf_armor slot group
    └── tags/item/wolf_armor_slot_items.json
```
