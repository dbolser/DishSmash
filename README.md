# DishSmash

A frantic dishwasher-stacking arcade game. Falling-block stacking, match-3 "nesting", and a kitchen schedule to keep up with.

**Play it: https://dbolser.github.io/DishSmash/**

Or open `index.html` in a browser. It's a single self-contained file with no build step, no assets and no network. Add `?seed=123` to the URL for a repeatable game.

## How to play

1. **Pick.** Dirty dishes arrive in batches on the **counter** (12 slots). Choose which one to load next. Swapping the falling dish for another counter dish is free once per dish; each further swap costs 10 points.
2. **Stack.** The dish appears in the drop chute just behind the back wall of the active rack, where you can move it freely, then falls toward the open door. Move it, rotate it and drop it. Drop points only count if the dish actually locks in place.
   - The **water tube** at the back centre of each rack sits above the rack: dishes slide under it as they fall, but nothing can rest on its own cell.
   - **Bottom rack:** plates, bowls, pots, pans, trays and boards. The back **plate tines** catch upright plates: an upright plate stops in the tines (x1.5) instead of falling through. Rotate a plate flat to lay it further forward.
   - **Cutlery basket** (front-right): cutlery only. Cutlery anywhere else is rejected.
   - **Top rack:** small items only (cups, mugs, glasses, wine glasses, small bowls, tubs, lids). Press **E** (or **Tab**) to pull it out over the bottom rack, and press it again to push it back in. Wine glasses on the left clip edge and mugs on the fold-down shelf score x1.5.
   - **Tall items** (frying pan, stock pot, baking tray, chopping board) block the top-rack cells above them; those cells show as hatched. A tall item can't go under a top-rack cell that is already occupied, and its landing preview turns red.
3. **Nest.** When 3 or more dishes of the same family touch side by side, they form a connected group and nest into one stack (at most 9 items). Families: dinner + side plates, cereal + small bowls, glasses + wine glasses, tubs + lids; every other dish only nests with its own kind. Cutlery needs 4 of the same kind. The stack takes the place of the front-most dish in the group, so the freed cells let the dishes behind slide forward.
   - **Streak:** every group that nests adds 1 to the streak, and the streak carries on to the next dish. A dish that locks without causing any nest resets it. Nest points are multiplied by 1 + 0.5 x (streak - 1), and the words climb NESTED! > SNUG! > TETRIS-TIGHT! > DISHWASHER WIZARD!.
   - Filling a whole rack row scores a PERFECT ROW bonus.
4. **Slam.** Press **S** to shut the door and start the wash (35 s). A quick wash (**Shift+S**) takes 12 s but scores x0.6.
   - Load score = item points x packing multiplier x load multiplier.
   - **Fullness** is measured by volume: the cells of every dish in the machine (nested dishes still count in full), plus top-rack cells blocked by tall items, divided by all usable cells. Nesting never lowers it.
   - A load at least 85% full earns the **FULL LOAD** eco bonus (x1.6). A load under 50% full is **Half-empty! Planet sad** (x0.5).
   - If nothing on the counter fits in either rack, the game says the rack is jammed: slam it.
   - While one machine washes, load the other one (**Shift+1** / **Shift+2**, or **`**). When a machine finishes and the one you are on is busy, the game switches to the free one.

The day runs from 06:00 to midnight in about 3.5 real minutes, with rushes at 07:00, 10:30, 12:30, 15:30, 18:30 and 21:00 (plus an 08:30 School Run from Day 4), and small trickles of dishes in between. Big rushes arrive in waves of up to 8 dishes, 5 seconds apart ("AND MORE!"). The strip at the top shows the next rushes with a countdown. Each day brings bigger batches (+15% a day), more frequent trickles and faster falling. Special days are Day 3 (Guests Over), Day 5 (Sunday Roast) and Day 7 (Birthday Party), and they repeat every week.

When the counter is full, dishes go into the **sink** (10 places). When the sink overflows, the **MESS** meter rises, and at 100% *the kitchen has fallen*. Mess drains slowly when the counter is under control. If too many dishes are dirty at once, the cupboard runs out of clean ones and a penalty event happens (for example, "Dad is eating soup from a mug").

Your best score and furthest day are saved in your browser.

## Controls

| Action | Keyboard | Touch / mouse |
|---|---|---|
| Move | Left / Right (or A / D) | drag on the rack, or the left/right buttons |
| Rotate | Up / X (clockwise), Z (anticlockwise) | tap the rack, or the rotate buttons |
| Soft drop / hard drop | Down / Space | the down button / swipe down or the drop button |
| Pick from counter | 1-9, 0 pick slots 1-10; or Q / W to select, then Enter (or F) | tap a card |
| Toggle top rack | E or Tab | Rack OUT / IN |
| Switch machine | Shift+1 (A) / Shift+2 (B), or ` to flip | tap the small machine, or Switch |
| Slam door (wash) | S, or hold Enter; Shift+S for a quick wash | SLAM! / Quick, or START on the door |
| Bin the falling dish back to the counter (-25) | B | Bin |
| Pause / mute | P or Esc / M | the buttons in the top bar |

The plain number keys pick from the counter because that is the most frequent action, so machine switching is on Shift+1 / Shift+2 (the game also switches for you when only the other machine is free).

## Test hook

`window.__dishsmash` gives deterministic control:

```js
const g = window.__dishsmash;
g.start(42); g.pause();        // seeded game, freeze the real-time loop
g.step(6500);                  // advance the simulation 6.5 s (fixed 60 Hz steps)
g.pick(0); g.move(-1); g.rotate(1); g.hardDrop();
g.toggleTopRack(); g.switchMachine(1); g.slam(/*quick*/ false);
g.forceRush('dinner');          // never the tutorial batch; dishes past 8 arrive in later waves
g.spawnBatch(['plate', 'fork', 'mug']);
g.state();                     // JSON snapshot, including ASCII rack grids
```

The hook also has `resume()`, `softDrop(n)`, `binBack()`, `render()` and `config`, which is the live CONFIG object.
