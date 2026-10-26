---
layout: page-fullwidth

title: "NEON OVERDRIVE: a cyberpunk escape game in your browser"
subheadline: "Steal a Roadster 2, outrun the SFPD, and get off the grid"
teaser: "A 2.5D chase through Neo San Francisco, built with Canvas 2D and zero external assets"
header: no
image:
    title: neon-overdrive-gameplay.webp
    thumb: neon-overdrive-gameplay.webp
    homepage: neon-overdrive-gameplay.webp

comments: true
categories:
- games
- javascript
- canvas
tags:
- game dev
- cyberpunk
- HTML5 canvas
- javascript
- web game
- procedural generation
- racing
---

<div align="center">
<a href="https://roadster-game.xeonqq.workers.dev/">
<h4><strong>Play NEON OVERDRIVE</strong></h4>
Free in the browser, no install
</a>
</div>

## The pitch

NEON OVERDRIVE is a 2.5D cyberpunk escape game set in San Francisco, 2088. You steal a Tesla Roadster 2, the SFPD seals the city, and the only way out is to collect four data cores, unlock one of the three bay gates, hit the pier ramp and land the car on the escape boat.

Most of the fun is in the middle: drifting through rain-soaked streets, jumping roadblocks, shooting police drones, and trying not to wrap the car around a building at 340 km/h.

<div align="center">
<img src="{{ site.urlimg }}neon-overdrive-menu.webp" width="80%" alt="NEON OVERDRIVE menu">
<br>
<em>The menu. One click and you are in.</em>
</div>

## What you do

* Steal four data cores from relay points around the city. The nav HUD and minimap point to the next one.
* Escape through one of three bay gates. Every route ends at the same cargo ship, and missing the deck puts you back on the pier.
* Drift to recharge turbo, then spend it on a 360 air jump or the rear jet boost.
* Jump roadblocks, spike strips and traffic, or shoot through them.
* Survive the escalation: patrol cruisers, pursuit cars, riot SUVs, drones and eventually a searchlight helicopter.
* Five weapons with limited ammo: Volt SMG, Breach-12, Hellfire, Arc Rail and EMP-9.
* Score from drift combos, near misses, air time and kills. Personal best is saved in the browser.

<div align="center">
<img src="{{ site.urlimg }}neon-overdrive-jump.webp" width="100%" alt="NEON OVERDRIVE air jump">
<br>
<em>Air jump over a blockade. A clean 360 landing gives some turbo back.</em>
</div>

## Under the hood

Plain HTML5 Canvas 2D and ES modules. No engine, no build step, no sprite sheets and no audio files. The city grid, 16 districts, cars, neon signs, rain, particles and synthwave soundtrack are all generated in code.

The Roadster is a lofted 3D mesh rather than a flat sprite, so the 360 flip keeps its volume. Buildings fade to a ghost cutaway when they would hide the car, and the game lowers its internal resolution when the frame rate needs help.

The whole thing was vibe coded with DeepSeek, GLM and Qwen, with me steering and testing.

## Controls

| Key | Action |
| --- | --- |
| `W` / `↑` | Accelerate |
| `S` / `↓` | Brake / reverse |
| `A` `D` / `←` `→` | Steer |
| `K` / `Ctrl` | Handbrake drift |
| `Shift` | Rear jet boost |
| `Space` | Air jump (26 turbo) |
| `J` / left click | Fire (auto-lock) |
| `Q` / mouse wheel | Switch weapon |
| `Esc` / `P` | Pause |
| `M` | Mute |

## Play

<div align="center">

<a href="https://roadster-game.xeonqq.workers.dev/">
<h3>Play NEON OVERDRIVE →</h3>
</a>

<a href="https://github.com/xeonqq/roadster_game">
Source on GitHub
</a>

</div>
