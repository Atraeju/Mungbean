# Mung Bean Farming Game 🌱💀

A browser-based incremental / idle game about growing, harvesting, juicing, and eventually **becoming one with the Mung** in a neon-drenched cyberpunk legume dystopia.

Everything runs in a single `index.html` file — no build tools, no external libraries.

---

## 🎮 How to Play

Open `index.html` in any modern browser (Chrome, Firefox, Edge, etc.).  
You’ll see your core stats at the top:

- **Sprouts** – Growing in the field over time.
- **Harvested Sprouts** – Sprouts you’ve collected.
- **Juice** – Produced from harvested sprouts.
- **Tokens** – The main currency.
- **Rare Mung Beans** – Endgame + prestige resource.
- **Time Played** – Seconds elapsed this run.
- **Prestige Level / Multiplier / Best Time** – Long-term progression.

### Core Loop

1. **Sprouts grow automatically** every 1125 ms.
2. **Harvest Sprout**  
   - Converts sprouts into harvested sprouts (more if upgraded).
3. **Juice Sprouts**  
   - Converts 1 harvested sprout → 5 juice.
4. **Sell Juice**  
   - Sells all current juice for tokens (rate can be upgraded & multiplied by prestige).

All actions and upgrades respect resource limits — no negatives, no “free” resources.

---

## 🛠 Core Mechanics

### Automatic Sprout Growth

- Every **1125 ms**, `sprouts` increases by:
  - `1` by default  
  - `2` with **Neon Grow Lamps**

Sprouts are visually shown as glowing green circles in the field. A soft cap keeps rendering smooth by limiting how many are visually drawn at once when totals skyrocket.

### Manual Harvest

- **Button:** `Harvest Sprout`
- Effect:
  - Harvests **1 sprout**, or **2** if **Larger Beans** is active.
  - Harvest amount is clamped so you never take more than you actually have.

### Juicing

- **Button:** `Juice Sprouts`
- Effect:
  - If you have ≥1 harvested sprout:
    - Consumes 1 harvested sprout  
    - Produces **5 juice**
- Juice is visualized as a rising liquid level in a bottle.

### Selling Juice

- **Button:** `Sell Juice`
- Effect:
  - Converts **all juice** to tokens:
    - `tokens gained = floor(juice × tokenPerJuice × prestigeMultiplier)`
  - Resets juice to zero.
  - Never produces negative values.

---

## ⬆️ Upgrades

### Core Upgrades

All purchased once (no duplicates). Buttons auto-disable once bought or if you lack tokens.

1. **Harvester (200 Tokens)**  
   - Unlocks automatic harvesting.  
   - Every second:
     - Performs 1 automatic harvest (plus extras from upgrades).

2. **Juicer (200 Tokens)**  
   - Unlocks automatic juicing.  
   - Every second:
     - Converts harvested sprouts to juice (1+ times, depending on upgrades).

3. **Larger Beans (100 Tokens)**  
   - Manual and automatic harvests collect **2 sprouts** instead of 1 (when available).

4. **Fertilizer (100 Tokens)**  
   - Every second:
     - **5% chance** to spawn a rare mung bean.  
   - Chance can be increased via prestige and **Neuro-Fertilizer Grid**.

---

### Cyberpunk Upgrades

All thematically grimy and dystopian, implemented as **multipliers**, not extra intervals (avoids timer spam).

1. **Neon Grow Lamps (300 Tokens)**  
   - Doubles sprout growth per tick:
     - 1 → 2 sprouts every 1125 ms.

2. **Drone Harvester Swarm (400 Tokens)** – *Requires Harvester*  
   - Harvester now performs **2 harvest actions per tick** instead of 1.

3. **Industrial Juicer Array (400 Tokens)** – *Requires Juicer*  
   - Juicer now performs **2 juicing actions per tick** instead of 1.

4. **Corporate Mung Syndicate (500 Tokens)**  
   - Base token rate becomes:
     - `2 tokens / juice` (before prestige multiplier).
   - In lore: you sold out to mung capital.

5. **Neuro-Fertilizer Grid (300 Tokens)** – *Requires Fertilizer*  
   - Doubles the base rare mung spawn chance:
     - 5% → 10% per second (before prestige bonus).

6. **Mung Milk Vat (600 Tokens)**  
   - Every second, if you have ≥10 juice:
     - Consumes 10 juice  
     - Converts it to:
       - Tokens (scaled by current token + prestige multiplier)
       - **+1 rare mung bean**
   - A late-game auto-conversion engine that pushes you to the win condition.

---

## 🎯 Minigame: Harvest Mode

- **Button:** `Toggle Harvest Mode`
- Shows a **green square** with a white circle (the “core”).
- Clicking the white circle:
  - Performs a harvest action (respecting Larger Beans).
  - If sprouts drop to 0, Harvest Mode auto-closes.
- Adds a small active-click mini-game layer for spice.

---

## 🧠 Prestige System (Corrupted Mung Ascension)

Once you collect **10 rare mung beans**, you:

1. Trigger the win state:
   - Full-screen overlay:
     - `"You have become one with the Mung!"`
     - Shows **time spent this run** and **current prestige level**.
2. Normal game loops stop; actions are disabled.
3. A button appears:
   - **“Ascend with Corrupted Mung (Prestige Reset)”**

### What Prestige Does

On each prestige reset:

- **Prestige Level +1**
- Run is fully reset:
  - Sprouts, harvested, juice, tokens, rare beans, time → 0
  - All upgrades (core + cyberpunk) are lost and must be re-purchased.
- Certain values **persist & improve**:

#### Token Multiplier

- Token multiplier:  
  - `1 + prestigeLevel × 0.25`
  - e.g. Prestige 0 → 1.0×, Prestige 1 → 1.25×, Prestige 2 → 1.5×, etc.
- Applied to all juice sales and Mung Milk Vat conversions:
  - `tokens gained = floor(juice × tokenPerJuiceBase × prestigeMultiplier)`

#### Rare Bean Chance Bonus

- Prestige adds a flat bonus to Fertilizer’s rare spawn chance:
  - `+2% absolute chance per prestige`, clamped to a max of 70%.
- Applies on top of:
  - Base 5% (Fertilizer)  
  - Or 10% if **Neuro-Fertilizer Grid** is purchased.

#### Best Time

- Tracks the **fastest run** to “become one with the Mung”.
- Shown as “Best Time to Become One With the Mung”.

---

## 🧪 Technical Notes

### File Structure

- Single file: `index.html`
  - Contains HTML, CSS, and JavaScript in one place.
  - No external dependencies.

### Main Intervals

- **Growth:**  
  - `setInterval` every 1125 ms → sprout generation.
- **Time:**  
  - `setInterval` every 1000 ms → timePlayed.
- **Harvester / Juicer / Fertilizer / Milk Vat:**  
  - Each has **one** `setInterval()` created only when the respective upgrade is purchased.
  - No duplicate intervals thanks to `hasX` flags and clean reset logic.

### Safeguards

- All resource-changing functions guard against negative values:
  - Harvesting and juicing clamp the consumption to available resources.
- Win state:
  - Clears all intervals.
  - Disables normal buttons; only prestige remains active.
- Prestige reset:
  - Clears intervals, resets state, restarts loops cleanly.

---

## 🕹 Controls / UX

- **Left click** buttons to perform actions.
- Hover upgrade buttons to see **lore tooltips**.
- The **Mung Log** panel at the bottom:
  - Shows flavor messages as you unlock upgrades, sell juice, generate rare beans, etc.
  - Newest events appear at the top.

---

## 🔮 Ideas for Future Extensions

- Multiple **win tiers** (e.g., 10, 50, 100 rare beans) with different titles.
- Additional **prestige currencies** (like “Mung Shards” or “Corporate Shares”).
- Achievements system with playful titles.
- Sound effects & light animations for harvesting / juicing / prestige.

---

## License

Do whatever you want with it.  
If you show it to anyone, tell them the **Mung** sent you. 🌱
