# FiveRank — FiveM server discovery, ranking and analytics

![Next.js](https://img.shields.io/badge/Next.js-16.3-000?style=flat-square&logo=nextdotjs)
![React](https://img.shields.io/badge/React-19.2-087ea4?style=flat-square&logo=react&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-5.7_strict-3178c6?style=flat-square&logo=typescript&logoColor=white)
![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-4.3-06b6d4?style=flat-square&logo=tailwindcss&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Supabase-3ecf8e?style=flat-square&logo=supabase&logoColor=white)
![Lua](https://img.shields.io/badge/Lua-FiveM_resource-2c2d72?style=flat-square&logo=lua&logoColor=white)

A ranking platform for 33,048 FiveM servers and 105,879 resources, built on
numbers the people being ranked cannot edit.

---

![Preview](https://youtu.be/EQPmfar_jsU)

## About this repository

This is a production platform, not a template, a theme, or a tutorial build.
Derives a public ranking from it, ships a Lua
game-server resource with its own in-game UI, and serves 66,551 indexable pages
on free-tier infrastructure.

Single developer, end to end: architecture, data pipeline, design, front end,
billing, the game resource, and the deployment.

## Overview

FiveM is Grand Theft Auto V's multiplayer modification, with tens of thousands
of community servers. Choosing between them is guesswork, and most directories
rank by whatever an owner types into a form.

FiveRank ranks by what is measurable.

- **Rankings** derived from the Cfx.re master list — live player counts an owner
  cannot inflate, because they do not control the source.
- **A resource index** of 105,879 scripts showing real adoption: how many
  servers run each one, and how many players are on those servers.
- **Studio pages** derived from resource naming conventions across the whole
  catalogue, so a studio has a page and an audience before anyone claims it.
- **Owner analytics** from an optional in-game resource, measuring what a public
  server list cannot see — session length, returning players, true peaks between
  polls.
- **Verified ownership** proved through Cfx.re rather than asserted, so a claimed
  server means something.

## Screenshots
![Hero](https://i.imgur.com/QBlH982.png)
![Servers](https://i.imgur.com/4txb9dN.png)
![ServerProfile](https://i.imgur.com/Q3xk7PY.png)
![Resources](https://i.imgur.com/3JJrxTS.png)
![Dashboard](https://i.imgur.com/RSkUuCs.png)
![Ingamepanel](https://i.imgur.com/TnuCjkX.jpeg)


## Tech stack

| Layer         | Choice                                | Why                                                                                                |
| ------------- | ------------------------------------- | -------------------------------------------------------------------------------------------------- |
| Framework     | Next.js 16 (App Router)               | Per-route rendering control — the difference between 66,551 pages that cache and 66,551 that don't |
| UI            | React 19, TypeScript 5.7 strict       | No `any`, no non-null assertions in application code                                               |
| Styling       | Tailwind CSS 4 (Lightning CSS)        | Design tokens in one place; no component library to fight                                          |
| Database      | PostgreSQL (Supabase)                 | One JSONB document for the store, real tables for the data that outgrew it                         |
| Driver        | postgres.js                           | Wire protocol, not a vendor HTTP API — the provider is one environment variable                    |
| Game resource | Lua 5.4 + NUI (CEF)                   | Runs inside FiveM; no framework dependency, MIT licensed, readable                                 |
| Auth          | Discord OAuth 2.0                     | The identity the audience already has                                                              |
| Payments      | PayPal subscriptions + one-off orders | Four plans across two ladders                                                                      |

## Engineering highlights

### A ranking nobody can buy

The entire value of a ranking is that it cannot be gamed. Public figures come
from the Cfx.re master list,
field-by-field without a protobuf library, because only three fields out of the
schema are needed.

Self-reported data from the in-game resource powers the owner's private
dashboard and **never** touches a public rank or player count. Everything in the
telemetry module was sent by the server it describes, by someone with an obvious
interest in the numbers being large.

That boundary is enforced mechanically rather than by convention: a test asserts
which modules are allowed to import it, so "rank by real player hours" cannot be
quietly added later. It is a rule nobody would remember in six months, which is
exactly why it is a test and not a comment.

### Telemetry that carries no identifier

The in-game resource measures unique players without ever transmitting — or
storing — anything that identifies one.

On join, the player's licence is hashed against a salt that exists only in
memory, the hash becomes a key in a set, and the licence is discarded. What
leaves the server is the _size_ of that set. No licence, no Steam ID, no Discord
ID, no name, and **no hash either**. Salts rotate daily and weekly and the
matching sets are emptied at the same moment, so nothing in memory can be tied
to anyone afterwards.

Restarts are handled honestly rather than hidden: counters reset when a resource
restarts, so the platform stores both bounds and shows `214–388 unique today ·
2 restarts` instead of one number that would be wrong.

### An in-game panel

A NUI panel rendered by CEF inside the game, opened by a console command or a
rebindable key. It shows the server's own standing — global rank, country rank,
players now, peak today, uptime, rating — served from a cached response, so a
hundred players opening it at once costs zero upstream requests.

It runs no timers and no animation loop while closed, blurs only the card rather
than the full screen, and degrades to a labelled honest state rather than a grid
of zeros when the server is unreachable.

## Work with me

I build web platforms, business software, and game-server tooling — from the data
pipeline to the pixels.

If you are hiring, or you have a project that needs someone who will own the whole
thing end to end, I would like to hear about it.

- **Website** — https://hristijanrafajlovski.com
- **Email** — info@hristijanrafajlovski.com
- **Discord** — lakicc

---

<sub>FiveRank is an independent project and is not affiliated with or endorsed by
Cfx.re, FiveM, Rockstar Games or Take-Two Interactive. Server names,
descriptions, icons and player counts belong to their operators and are surfaced
from the public Cfx.re server list.</sub>
