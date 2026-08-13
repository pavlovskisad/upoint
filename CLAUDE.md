# upoint — landing page

Single-file landing for **upoint**, a Ukrainian lifestyle club in Vienna (`@upointvienna`).
Everything lives in `index.html`. No build step, no dependencies. Drop it at the repo root
and Vercel serves it as-is.

-----

## 1. The client

Ukrainians who moved to Vienna after the full-scale invasion. They opened as a shop for
Ukrainian brands plus a room to gather in, called themselves an “aesthetic space”, and then
outgrew that: tours, lectures, concerts, a flea market, brands that are no longer only
Ukrainian, and an audience much wider than the founding crowd. They are repositioning as a
**lifestyle club**. This page is the hub people land on before anything else.

### Live destinations

|Word (EN / UA)        |Goes to          |URL                                     |
|----------------------|-----------------|----------------------------------------|
|things / речі         |flea market      |`https://t.me/andrrit2`                 |
|spot / місце          |the door         |Maps search on Bernardgasse 7/4, Wien   |
|experiences / враження|events, tours    |`https://www.instagram.com/upointvienna`|
|calendar / афіша      |in-page schedule |no URL — opens the `#cal` panel         |
|talks / розмови       |lectorium        |`https://t.me/Upointlecture`            |
|community / спільнота |announcements    |`https://t.me/upvienna`                 |

Six words. Mapping lives in `ITEMS` and `LINKS` at the top of the script.

**`spot` carries the address as its tag line**, so the street and number are readable without
tapping anything, and the tap opens Maps. It is a Maps *search* on the address rather than a
place link, because no place ID was supplied; a search resolves from any country and hands
off to the native app. If the club sends a place link later, swap `LINKS.spot` for it — a
place link is more precise and survives the address being mistyped.

**`calendar` has no URL.** It opens a panel filled from the `EVENTS` array, which ships empty,
so today it shows its empty state: *no dates up yet, they go to the Telegram channel first*,
with a link through. Rows dated before today drop out on their own — a list nobody has touched
in a month degrades to the empty state instead of advertising last month's events. That is the
floor, not the ceiling: a hand-edited array in a landing page is only honest while someone
remembers to edit it. See §11.

-----

## 2. Brand

|      |                          |
|------|--------------------------|
|Deep  |`#aa6c10` — Pantone 146 C |
|Bright|`#f7be00` — Pantone 7408 C|
|Ink   |`#111`                    |
|Paper |`#fff`                    |

**Colour logic.** Brown is the club: the mark, the roots, and the point while it belongs to
them. Yellow is what grew: trunk, branches, twigs. Nothing else gets a colour.

**The dot carries the brand**, and that is the spine of the sequence:

|Beat                    |Colour                    |
|------------------------|--------------------------|
|opening, before any tap  |`GPS_RGB` — map-pin blue |
|the tap, the beat after it, the hop, and the landing|`INK_RGB` — yellow, snapped, not eased|
|the first stretch of the stroke|morphs to `BARK_RGB` — brown, while moving|
|the rest of the mark     |`BARK_RGB` — brown       |
|off the mark, through about, down to the root|`INK_RGB` — yellow|
|on the tree              |`BARK_RGB` — brown       |

The tap **snaps** to yellow rather than easing into it: blue easing to yellow passes through
olive and reads as mud. The yellow then holds — through a beat before anything moves, and
through the whole hop — so the brand colour arrives on the paper before any ink does. The
morph to brown happens where it can actually be watched, with the dot sitting still on the
left tip, and runs slower than the standard crossfade (`dot.colK` drops to `0.09` for it).

It is brown while it paints the mark because there it *is* the mark in motion. It turns yellow
the instant it pops off the right tip and stays yellow through the about page and down to the
root, then goes brown again as it takes to the tree. The two brand colours are not decoration;
they mark which half of the story the point is in.

**The blue is the one deliberate exception** to “nothing else gets a colour”. Before the first
tap the dot is a map pin — blue, with a pulsing accuracy ring (`dot.gps`) — so the opening
screen reads as *you are here* rather than as a stray dot, and the first tap is the moment the
point stops being a map marker and becomes the club's. The colour is dropped the instant that
happens and never returns until `restart()` puts you back at the opening.

`dot.col` eases per channel toward `dot.colT`, so any colour can follow any other; the beats
are set in `goLogo()`, `go()` and `restart()`.

**The about page is the dot, opened out.** On landing it grows into a yellow disc that covers
the screen (`flood`), the copy is knocked out of it in white, and on the way to the tree the
whole page collapses back into the dot it came from. That is the one place yellow is a field
rather than a stroke, and it is allowed because the field *is* the point — not a background
someone chose.

**Type.** The brand board is a geometric sans with a double-storey `a`, most likely Gilroy
or Sofia Pro. Both are commercial. The stack falls back to Figtree off Google Fonts. Buy the
real face, self-host `woff2`, drop the Google `<link>`.

**The mark.** The real one, traced out of `Logo_U.Point aesthetic space.ai`. It is **not** a
sine and not a constant-width stroke: the width runs from 18 to 21 units against a 182-unit
span, the ends are flat angled cuts rather than round caps, and the first dip closes into a
tight inner corner no offset curve reproduces. So the mark is a **filled outline**, not a
stroke.

Two constants carry it, both in local space where x spans `[-0.5, 0.5]` across the mark and
y uses that same scale factor, which makes `logo.scale` the drawn width in px:

|            |                                                                       |
|------------|-----------------------------------------------------------------------|
|`MARK_D`    |the outline, filled via `MARK_PATH` (a `Path2D` built once at load)    |
|`SPINE`     |its centreline → `LOGO_LOCAL`, what the dot paints and roots hang off  |

The centreline was recovered by splitting the outline at its two straight cap segments,
resampling both sides by arc length, and averaging paired points; the cap midpoints are the
true endpoints. Redo that if the mark is ever redrawn — do not eyeball it.

**`WRATIO` is 0.1148**, the median stroke width over the mark width. That one constant drives
the dot size, the trunk width, every taper, and the root widths, so the tree is noticeably
heavier than it was under the old approximation (0.085). If the tree ever needs slimming,
change the taper in `growTree()`, not this.

The paint-on reveal (`clipPainted()`) is the nib's sweep: discs along the spine, ending on the
dot's **sub-sample** position rather than the nearest sample. Two measured constants size it,
and the gap between them is the whole trick:

|            |        |                                                              |
|------------|--------|--------------------------------------------------------------|
|`MARK_HW`   |`0.0498`|widest the stroke gets **perpendicular** to the spine          |
|`MARK_REACH`|`0.0828`|farthest any outline point sits from the **nearest** spine point|

`MARK_REACH` is much the larger because the mark's corners jut out at the bends. Size every
disc at `MARK_HW` and 364 of the outline's 1261 points are farther from the spine than that,
so they never get revealed at all — the corners stay cut until the clip is dropped and the
logo visibly snaps into shape at the end. Size every disc at `MARK_REACH` and the ink runs
ahead of the dot instead. So the discs are **wide behind and narrow at the nib**: `MARK_REACH`
from 14 samples back, tapering to `MARK_HW` at the dot. A corner finishes filling a few dozen
px behind the brush, which reads as ink settling rather than as a pop.

Three earlier versions were worse and are worth not repeating. A full-height vertical curtain
crossed the steep parts of the mark at a shallow angle and dropped whole sections in ahead of
the dot. A straight cut square to the stroke fixed that but clipped ink the brush had already
laid on the inside of a bend, which reads as the drawing lagging the dot. A constant round
brush fixed *that* but cut the corners, as above. The disc chain still starts behind the first
spine point so the brush does not eat the mark's angled left cut, and at `logo.prog >= 1`
there is no clip and the outline is exact.

**The nib drowns in its own line.** `dot.sub` sinks it between `logo.prog` `0.22` and `0.80` —
off the paint's own progress, not a timer, so it cannot drift out of step — leaving the middle
of the stroke to draw itself.

It sinks by **shrinking, not fading**. At `R` the nib is 1.15× the stroke's half-width, so it
shows; at half that it is 0.58× and sits entirely inside the ink, hidden by being narrower
than the line while staying fully opaque. Fading was tried first and is worse: a
part-transparent nib overhangs the stroke onto bare paper and reads as a pale smudge, which
forces the dive to be quick to hide it. Shrinking has no such artefact, so it can take as long
as it likes — `0.07` per frame, about two thirds of a second each way.

Nothing else depends on the nib being visible: the reveal already ends in a disc the width of
the stroke, so with the nib sunk the line simply ends in a clean round cap.

Sizing the nib itself matters too: it is plain `R` while painting. It was briefly `R*1.18`, to
cover a straight cut that no longer exists, and at that size it is 36% wider than the stroke
and reads as a blob riding the end of the line.

-----

## 3. The concept

Their space is called upoint. A point that moves draws a line. That line is their logo. So the
visitor **is** the mark in motion, and moving through the site grows the thing.

The metaphor is a rhizome rather than a tree in the strict sense: no single centre, lateral
spread, separate shoots that turn out to be one organism underground. That maps onto a
diaspora community that started as one shop and now runs in five directions. The page reads
as a tree because a trunk and a canopy are legible in two seconds; the roots underneath carry
the rest of the idea.

-----

## 4. Interaction spec

Five states. Tap, click, swipe, wheel, arrow keys, space and enter all advance.

|Phase      |What happens                                                                                                                                               |
|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
|`start`    |Empty paper. The dot lands centre as a blue map pin, accuracy ring pulsing, and idles. Nothing else on screen.                                             |
|`logo`     |The pin snaps yellow, holds a beat, hops to the left tip, and paints the mark in that same yellow. The first ink then soaks in rather than appearing — the mark's flat angled left cut is entirely inside the brush's first disc, so it used to pop in whole on touchdown, with the landing splat flattening the dot so it could not even cover it. The nib sinks under its own ink at `prog 0.03`, surfaces at `0.82`, then pops off the right tip: the separation beat.|
|`logoDone` |Idles beside the tip. UA/EN appears. The paper collar carries the separation beat here — the dot and the mark are the same colour, so the white gap is the only thing telling them apart.|
|`about`    |The dot squats and launches top-left **as the mark drops to the footer on the same frame** — the leap pushes the logo down. It then opens out into a yellow page; the copy writes itself in, in white.|
|`aboutDone`|Idles.                                                                                                                                                     |
|`menu`     |The page collapses back into the dot, the copy flies off the top, and the dot drops to the base of the tree on the footer mark, still yellow. Six words appear.|
|—          |First swipe: trunk climbs **and** the root system pours out under the mark and walks off the bottom edge. Both at once.                                    |
|—          |Each swipe launches the dot to that destination while the branches to it paint themselves, and shows the link.                                             |

**Directions in menu.** Nothing is hardcoded: `pickByAngle()` matches a swipe to whichever tip
sits closest to its angle, measured from the centre of the tip cluster. Six words do not fit
four directions, and the angle match means they do not have to — a swipe toward a word selects
it, which is the rule the original four-direction map was only approximating.

Angles are measured from the cluster centre rather than the tree base because every tip is
above the base, so base-relative angles would crush all six into the top half.

The sides each tip grows on are **fixed**, not random — the swipe that reaches a word has to
mean the same thing on every visit. Only the jitter moves. Measured over six sessions:

|Word       |Sits at|Reached by|
|-----------|-------|----------|
|experiences|-91°   |up        |
|calendar   |-50°   |up-right  |
|talks      |4°     |right     |
|community  |68°    |down      |
|spot       |124°   |down-left |
|things     |180°   |left      |

The four original words keep their cardinal swipes; the two new ones answer the diagonals.
Arrow keys cover the four cardinals only — the other two are a tap on the word or the Index,
which is one more reason Index is non-negotiable.

**Label placement.** A word has to read as belonging to its branch, so `placeMenu()` puts it
on its tip: beside the tip when there is room outboard, otherwise just above it. It never
slides a label to the margin — that was tried, and a label parked at the edge while its
branch sat 150px away read as unrelated to the tree.

**One or two words per session sit *on* their branch** rather than clear of it, so the limb
grows straight through the label — `TREE.through`, picked once per tree in `growTree()`. All
six would read as sloppy placement; one or two reads as the tree taking the page over. The
words are HTML above the canvas, so the branch passes behind the text and nothing is lost.

What made that hard is that the label used to be as wide as its **tag line**, and
`telegram · lectorium` is far wider than `talks`, so fitting the tag dragged the word off its
branch. The tag is now `position:absolute`, out of flow: a label measures the width of its
word alone, and the tag runs toward the middle of the screen (`inL` / `inR`), where there is
always room for it. Only the word has to fit anywhere near its tip.

**The push-off.** Leaving the logo is one gesture with two halves: the dot squats, then
launches up-left while the mark drops to the footer starting on the same frame. Both run the
same duration, and the mark takes the `eBack` overshoot, which lands as the impact. The flight
uses `eOut` rather than the default `eIO` — with `eIO` the dot crawls out of the squat while
`eBack` drops hard, so the logo looked like it moved first even though both started together.

**Back to top.** The sequence only ran forwards, so the tree was a dead end short of a reload.
The `↑` control appears only in `menu`, the one phase with no way out. `restart()` is a
transition, not a cut: the dot pops off wherever it stands and flies home to its opening
position while everything grown dissolves behind it (`fade`, a global alpha on the drawing
only — the dot stays solid throughout). Nothing is torn down until the dot lands, so the
visitor arrives at the opening state rather than being dropped into it.

It clears rather than preserves the tree. The next run grows a different one regardless, and
coming *back* to a half-explored tree reads as broken rather than clean.

**Index is currently off** (`INDEX_ON = false`), at the client's request. Flip it back to
`true` and nothing else needs changing — the button and the sheet are both still wired.

It is worth knowing what is switched off. Index listed all six as plain text at every stage
after the logo: it was the only route that handed someone a link in two seconds without
playing the sequence, and the only keyboard path to `spot` and `calendar`, which the four
arrow keys cannot reach. With it off the tree is the sole way in, so anyone arriving for the
lectorium link has to play to get it. Fine for a launch that is mostly about the feel; watch
the analytics in §11 before deciding it is permanent.

-----

## 5. Growth rules

The framework is a tree; the instance is unique per session. These are ordered by how much
each one contributes to reading as a tree.

1. **Acyclic.** Every stroke starts on an existing stroke and ends in open space. Nothing ever
   joins two existing points. Break this rule and the drawing collapses into a blob within
   three swipes. This is the whole thing.
1. **The dot jumps, the tree draws itself.** Selecting a word starts every undrawn segment on
   the route painting itself, cascading outward, while the dot flies straight to the tip and
   lands as the last one arrives. It used to walk and retrace, laying the ink as it went;
   that was truer to §3 but slow by the fifth swipe, and jumping tested better. The route
   still decides which segments exist, so rule 1 is untouched. Jumping skips the retrace, and
   retraced segments were what sprouted the spare twigs, so `goJump()` sprouts on skipped
   steps at the same rate — otherwise the tree fills in visibly thinner.
1. **Taper.** Each generation is 0.62 of its parent’s width, floor 0.16. The trunk starts
   slightly fatter than the logo stroke.
1. **Angle.** 22–44° off the parent, side alternating, 8° jitter.
1. **Length.** A child runs 30–52% of its parent.
1. **One bend per branch.** A single arc, never an S. S curves read as vines.
1. **Light.** Every direction is pulled toward vertical by 0.22.
1. **Spacing.** Twigs sit on the outer 65% of a parent, minimum 0.13 apart along it.

**Roots** run the same rules with three changes: gravity replaces light (pull `+0.30` down),
steps are shorter with one more fork level, and anything crossing the bottom edge is cut off
mid-width instead of tapering. The cut-off is what sells “continues below”. Fine hair roots
that stay in frame do taper, so both readings are present.

**Roots have to leave the mark, not hang off it.** Three things do that, and all three matter:
they start *on the spine* rather than below the mark, so their round start caps are buried
inside the stroke; they start near the mark's own width (`LW*0.66..0.88`) and taper downward,
rather than starting thin; and they fan from the first step (`lat*rnd(0.70,1.45)`) rather than
dropping straight down. Steepening that first step was tried and is wrong — every root then
plunges into a narrow onion under the trunk and the mark stops looking like something they
spread from.

**The footer mark is drawn at `markW*0.58`.** It was `0.30`, which put its stroke at a quarter
of the trunk's width and the same weight as a single root, so the logo vanished into its own
root crown. If it ever needs to shrink again, thin the roots to match — the mark has to stay
the heaviest brown thing down there or it stops reading as the source.

**Fixed per session:** trunk, low fork, high fork, six tips, and which side each tip grows on.
**Regenerated per session:** trunk lean, fork heights, limb angles and lengths, bend direction
and depth, twig sides, root count (5–7 primaries), which primaries become taproots.

Tip *sides* used to be partly random — `community` picked a side per session. With six words
the swipe angles have to stay put, so sides are fixed and only the jitter moves.

-----

## 6. Code map

One file, top to bottom:

```
content        LINKS, ITEMS, COPY            all client-facing strings
the mark       MARK_D, MARK_PATH, SPINE      traced outline + its centreline
canvas         layout(), footerPose()        sizing, DPR cap at 2
the dot        dot{}, travel(), hopIdle()    jelly spring + path following
the tree       growTree(), sprout()          skeleton + twigs
roots          growRoots(), rootStep()       recursive, capped at 68 strokes
colour         dotFill(), flood{}            the dot's brand colour, and the about page
ink            ribbon(), inkStroke()         tapered fills
               drawLogo()                    fills MARK_PATH, clipped while painting
               bakeStroke()                  moves finished ink to the offscreen layer
routing        chainOf(), route()            which segments lie between two tips
phases         goLogo/goAbout/goMenu/go()    the sequence
               goJump()                      dot flies, branches paint themselves
               restart()                     back to top, dissolves and flies home
input          pointer, wheel, keyboard
frame()        render loop
```

**Render order per frame:** clear → baked layer → live strokes → **mark** → flood → dot. The
mark goes on top of everything grown, which is what makes the tree read as coming out of it:
the trunk climbs from behind it and the roots run down under it, so the logo stays whole
instead of being painted over by its own tree. Everything up to the flood is drawn at
`globalAlpha = fade`, which is 1 except during `restart()`.

**Self-painting segments.** A segment with a `t0` is drawing itself on its own clock, exactly
as twigs and roots already did; `frame()` advances it and fires its `onDone`. Nothing drives
`seg.prog` from the dot any more.

**Smoothing.** Every stroke renders as a chain of quadratics where each sample point becomes
a *control point*, not a vertex. That is why there are no corners. Do not replace it with
`lineTo`.

**Tapering.** `ribbon()` walks the centreline, offsets each point along its normal by half the
local width, and fills the closed outline with round caps at both ends. The mark does not go
through `ribbon()` at all — it ships as its own drawn outline and is filled directly, which is
the only way to keep the angled ends and the tight inner corner.

**Baking.** Once a stroke finishes it draws into an offscreen canvas and stops being
recomputed. Without this the phone starts dropping frames around stroke sixty (six branches +
44 twigs + up to 68 roots).

**Jelly.** One spring on a single scalar `dot.s`, rendered as a volume-preserving ellipse.
Positive `s` with `ang = 0` is wide and flat. Squat before takeoff, stretch along the flight
direction, splat flat on landing, wobble out. `k = 0.26`, `damping = 0.24`.

The dot’s radius also tracks the width of whatever branch it stands on, clamped to
`[0.58R, 1.20R]`, so it reads as pressure on a brush.

-----

## 7. Tuning

|What                             |Where                                                                    |
|---------------------------------|-------------------------------------------------------------------------|
|Everything downstream of the mark|`WRATIO`                                                                 |
|Tree proportions                 |`growTree()` — the `H*0.70`, `H*0.47`, `H*0.42`, `H*0.175` node heights  |
|Twig density                     |`goJump()` — `k = 1 + (Math.random()*2.4)` per finished segment          |
|Twig ceiling                     |`sprout()` — `twigs.length > 44`                                         |
|Root spread                      |`growRoots()` — primary count, `lat` multiplier, taproot threshold `0.34`|
|Root ceiling                     |`rootStep()` — `roots.length > 68`                                       |
|Branch paint speed               |`goJump()` — `clamp(seg.len*1.5, 300, 760)`, and `0.55` for the overlap  |
|Leap speed                       |`goJump()` — `clamp(d*1.35, 400, 900)`                                   |
|Jelly                            |`dot.sv += (-dot.s*0.26 - dot.sv*0.24)`                                  |
|Colour crossfade speed           |`dot.colK`, default `0.14`; `0.09` for the yellow→brown morph             |
|First-ink soak                   |`goLogo()` — `wait(330)` then `tween(280, …)` on `logo.ink`               |
|About page open / collapse       |`goAbout()` — `tween(600, …)`; `goMenu()` — `tween(540, …)`              |
|Push-off sync                    |`goAbout()` — `PREP`/`FLIGHT` drive both the leap and the mark's drop     |
|Map-pin ring                     |`dot.gps`, and the `2200`ms pulse in the dot's draw                       |

-----

## 8. Known nuances

- **iOS address bar.** The resize handler ignores height-only deltas under 130px on touch
  devices. Without that guard, one address-bar collapse rescales the tree mid-session.
- **Safe areas.** `viewport-fit=cover` plus `--sat` / `--sab`. The footer mark clears the home
  indicator by `SAB * 0.7`.
- **Resize rescales, never regenerates.** `rescaleTree()` and `rescaleRoots()` multiply
  coordinates and widths, unbake everything, and let it redraw. A regenerate would wipe what
  the visitor grew.
- **Google Fonts is a single point of failure.** Self-host.
- **No storage anywhere yet.** Every visit starts from bare paper.
- **`prefers-reduced-motion`** collapses every duration to ~0 and keeps the sequence
  navigable. Test it.
- **Not yet tested:** Firefox, Safari desktop, landscape phone, very tall or very wide
  viewports. `Path2D` and `ctx.ellipse` are the only APIs worth checking.

-----

## 9. What was tried and dropped

- **v1, radial rhizome from a centre seed.** Eight roots out to eight labelled nodes, click
  anywhere to grow a tendril from the nearest point. Dropped: crowded, and branches sprouting
  from wherever you happened to click read as noise rather than one organism.
- **v2, scene sequence with point-to-point branches.** The sequence was right and survives.
  The branches were not: drawing straight from the dot to the target closed loops, and by the
  third swipe the screen was a filled yellow blob.
- **v2 stroke sampling.** The trail recorded the dot once per frame, so the fast middle of a
  swipe left 15–20px chords that read as hard corners at 24px stroke width. Now the trail
  follows the path itself at ~4px resolution.

-----

## 10. Open with the client

1. **About copy is placeholder and mine.** Needs their facts: opening year, rough headcount
   now, and how they want the Ukrainian-founded-but-open-to-everyone point made.
1. **German.** Vienna audience is mixed. UA/EN/DE, or leave it at two?
1. **Do events and tours deserve their own words**, or stay inside `experiences`?
1. **Confirm the four-word set.** `things / experiences / talks / community` is a proposal.
1. **The licensed font file.** The mark is done — the face is still Figtree off Google Fonts.
1. **The wordmark.** The `.ai` carries `Point / aesthetic space` set as outlines next to the
   squiggle. The page uses the squiggle alone, since “aesthetic space” is the positioning they
   are moving away from. If they want a lockup, it needs new wording first.

-----

## 11. Roadmap

**Shared root system.** The strongest version of this: write every branch and twig anyone
grows to a table, load the accumulated shape on the next visit. After six months the landing
page is a dense organism that a few thousand people grew together, and the club can project
it in the space, print it, put it on totes. Schema is small: `{ session, kind, points[], w0, w1, created_at }`. Rules 1 and 2 already guarantee anything anyone adds stays tree-shaped, so
the accumulation cannot degrade into noise. Cap render at the most recent N strokes plus the
skeleton.

**Live presence.** Other visitors’ dots moving on the same drawing. Fun for forty seconds,
worth far less than the accumulation. Build it second, if at all.

**Real content behind the words.** Four of the six still exit to Telegram and Instagram. A
shop would change the architecture, so decide before building one.

**The schedule, properly.** `EVENTS` is a hand-edited array in `index.html`. It is honest
about being empty and it expires its own rows, but it still depends on a person remembering
to edit a landing page. The moment there is a real cadence of events, move it: a public
Google Calendar or Luma feed read at load, or a small table alongside the shared root system.
Watch for the failure mode first — if the array sits empty for a month while events run on
Instagram, the word `calendar` is doing damage and should point at Instagram until it is fed.

**Analytics.** Which direction people swipe first, how many reach `menu`, how many use Index
instead of the tree. That last number tells you whether the game is a delight or a tax.