<div align="center">
<img src="https://i.ibb.co/YFtvhCpY/piz.png" width="140">
  
# Sukkit

[![PandaSpigot](https://img.shields.io/badge/PandaSpigot-Fork-red)](https://github.com/hpfxd/PandaSpigot)

[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/nqtsu91)

</div>

**Sukkit** is a custom Minecraft **1.8.9 server fork** built for competitive,
ability-based PvP game modes. It extends
[PandaSpigot](https://github.com/hpfxd/PandaSpigot) (a high-performance,
Paper-lineage 1.8.8 fork) with the low-level hooks that power plugins actually
need: full control over the damage formula, potion lifecycle events,
server-side player clones, and pluggable world generation.

---

## What the fork adds

The Sukkit changes live as patches on top of PandaSpigot
(`pandaspigot/patches/`, the `Sukkit:` prefixed ones).

### Combat & damage control

- **`PlayerReceiveDamageEvent`**: a single authoritative hook over the whole
  1.8 damage pipeline. Plugins can inspect and rewrite every component of a
  hit (base damage, Strength/Resistance contributions, armor, enchantments,
  absorption, critical bonus) instead of guessing from the final number.
- **`PlayerFatalHitEvent`**: fired when a hit would kill. Cancelling it allows true death control.
- **`EntityThornsEvent`**: intercept and rescale Thorns returns.
- **`PlayerAttackDamageSource`** tracking and silent-damage support on
  `LivingEntity`, apply damage without knockback/i-frame side effects

### Potions & effects

- **`PotionAddedEvent` / `PotionRemovedEvent`** , cancellable events for when an EntityPlayer receive or loose an effect
- **`PotionEfficiencyManager`**, temporarily scale how strong a given effect
  is on a given player (e.g. "reduce the target's Resistance efficiency by
  half for 10 s") without touching the effect itself.
- **`PlayerArmorChangeEvent`**, react to armor swaps

### FakePlayers (server-side clones)

A complete fake-player stack (`EntityFakePlayer`, `FakeNetworkManager`,
`FakePlayerConnection`, `FakePlayerManager`) spawns player-like entities that
exist purely server-side, with no real connection and their own event family:
`FakePlayerSpawnEvent`, `AttackEvent`, `DamagedEvent`, `DeathEvent`,
`KillEvent`, `EatEvent`, `EffectEvent`. Clones fight, take damage and die
without ever being confused with real players.

> This is largely inspired by [BotUHC](https://github.com/CrystallicDev/BotUHC)

### Pluggable world generation

Hooks designed for custom-dimension plugins (see *[Izanami](https://github.com/CrystallicDev/Izanami)*):

- per-world **NMS generator replacement**;
- **client-side biome remapping**, so custom biomes render as chosen vanilla
  ones on unmodified 1.8 clients;
- **replaceable carvers** in the vanilla generator;
- **custom block behaviors**.

### Inherited from PandaSpigot (and backports)

Modern-ish tooling on a 1.8.8 core: performance work throughout, bundled
**Adventure** text API, modern explosion API, `PaperServerListPingEvent`, and
more — see the upstream README in `pandaspigot/`.


## Building the fork

Everything goes through the `panda` script at the fork root:

```bash
cd pandaspigot
./panda patch     # apply all patches to the source trees (no build)
./panda jar       # apply patches and build → paperclip.jar
./panda rebuild   # export your committed changes back into patches/
```

Sukkit-side changes are committed in the patched source trees
(`PandaSpigot-Server`, `PandaSpigot-API`) and exported back with
`./panda rebuild`, keeping the fork rebasable on upstream PandaSpigot.
