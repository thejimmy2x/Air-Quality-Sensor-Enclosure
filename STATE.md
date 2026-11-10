# STATE — Sensor Stack Enclosure

**Project:** Sensor Enclosure (JDP3D) | **Owner:** Jimmy Pezzone
**Active track:** enclosure-v1
**Last updated:** 2026-07-15

## Current state
Working doc = **"Sensor_Case"** in Fusion (NOT saved — Jimmy saves manually).
Doc is now MULTI-COMPONENT: root has 3 occurrences — **Base** (body BaseShell),
**Tray** (TrayBody), **Lid** (LidBody), each its own component. (A separate
"Sensor_Case_snap" doc exists — DISREGARD it, not canonical.) v1 + fit-check
revisions applied:
- Tray + PMS5003 FIT CONFIRMED good (physical).
- Corner-post clearance scallops added; PMS intake raised; ESP/SGP share a wall.
- Gas-sensor bays reworked: side-wall wire channels + enlarged SCD-40 (see below).
- **PMS 4-hole air inlet ported (2026-07-01, see below).**
- **ESP USB port widened to dual-USB-C slot (2026-07-02, see below).**
- **BASE PRINTED PERFECT (2026-07-03): both port fixes (PMS inlet slot + ESP
  dual-USB slot) physically CONFIRMED good.**
- **Sensor retention clips added (2026-07-15, see below).** Doc now has 5
  components: Base, Tray, Lid, **Clip_SCD40**, **Clip_Gas** (2 occurrences).
  Tray gained 12 snap windows. NOT SAVED / NOT EXPORTED yet.
Next: physical fit-test of the 3 gas sensors in the reworked bays — this also
settles PCB_T, which drives the clip press height. Base exported
(Sensor_Case.stl) + pushed to GitHub main (commit 3f538e9).
Full plan: `C:\Users\jdp63\.claude\plans\i-have-a-sensor-quizzical-owl.md`

## Model summary (Fusion, 55 user params driving geometry)
- External size: **155.2 × 83.2 × ~39.4 mm** (base 32.4 tall + lid 7).
- Interior: 150 × 78 × 30 mm.
- **Base** (comp Base / body BaseShell): filleted shell, chamfered floating base,
  4 corner insert bosses (Ø8, 4.1mm pilot for M3 heat-set), back-wall ports
  (PMS fan Ø22 @ X25 + PMS 4-hole inlet slot @ X50/Z23.4 + ESP dual-USB-C slot
  @ X113-137/Z5.4-12.4), honeycomb
  vents front + both sides, LED light-pipe port. NOTE: PMS vent face has ONLY a fan
  + a 4-hole inlet row (no separate exhaust — old "rect exhaust" was a misconception).
- **Tray** (1 body): floor 2.5mm; 4 corner-post scallops (r=5.5, clears Ø8 posts);
  3 gas-sensor bays with wire channels on BOTH side (±X) walls — SCD40 26.5×23.8,
  BME680/SGP40 26.4×18.8; PMS cradle (open back → intake), ESP cradle (open back →
  USB), 2 front finger scallops. Full length 149.5 (0.25mm/side to cavity).
  NOTE base intake circle raised +2mm so tray floor no longer blocks it.
  **+ 12 snap windows (6.0 x 1.8 @ Z5.8-7.6) in the gas-bay walls for the
  retention clips (2026-07-15).**
- **Clip_SCD40** (1 body) + **Clip_Gas** (1 body, 2 occurrences) — sensor
  retention clips, snap into the tray windows. See section below.
- **Lid** (1 body): 3mm plate, matching fillet + top chamfer, register lip (0.4mm
  clr, corner-notched), 4 M3 screw counterbores aligned to inserts.

## Board layout (mm, interior min-corner x0,y0)
scd(12,12) bme(50,12) sgp(85,12) | pms(12,38) esp(112,6). Corner bosses (4,4)(146,4)(4,74)(146,74).
Thermal zoning: hot back (PMS+ESP) vs cool front (gas sensors); SCD40 front-left, farthest from ESP.

## Exports (X:\JDP3D\sensor enclosure\export\)
sensor_enclosure_assembly.step + base/tray/lid .3mf + .stl (High refinement).

## Gas-sensor bay rework (2026-07-01, after tray/PMS fit confirmed good)
Tray + PMS5003 now fit perfectly. Reworked the 3 gas-sensor bays:
- WIRE CHANNELS were on the wrong wall (back +Y). Real Adafruit boards have
  STEMMA-QT connectors on LEFT+RIGHT (+/-X) edges. Solid side walls were also
  blocking the connectors from seating -> that was the "bays too small" symptom.
  Fix: channels moved to BOTH side walls (8mm wide, Y17-25), all 3 bays
  ("both ends wired out"). Old back notches removed.
- SCD-40 genuinely too small: board 25.5 x 22.8mm, bay was 19 deep. Enlarged
  bay to 26.5(X) x 23.8(Y), grown front+back; back wall clears PMS cradle 1.40mm.
- BME680/SGP40 bays set to 26.4 x 18.8 (fits 25.4 x 17.8 std footprint).
- SGP40 right channel breaches the shared SGP/ESP wall (routes to ESP).
- Implementation: DELETED old pocket joins+notch cuts (Extrude3-8, Sketch3-8),
  REBUILT 3 pockets (Pocket_SCD40/BME680/SGP40) + 6 channels (Chan_*_L/R) on
  the Z=4.9 floor-top plane (Plane2). Rebuild needed because sketches are
  unconstrained rects (can't edge-drag cleanly). Single valid body, all healthy.
- Board dims: SCD40 25.5x22.8 (Adafruit 5187, confirmed); BME680/SGP40 taken as
  25.4x17.8 std STEMMA-QT (Adafruit hides exact dims in image-only fab prints).
  VERIFY BME680/SGP40 footprints at physical fit; enlarge if needed.

## PMS5003 four-hole air inlet — ported (2026-07-01)
Problem: base back wall had NO opening for the PMS's 4 clean-air INLET holes (only
the fan Ø22 + a mislocated "exhaust" rect). Per datasheet the vent face has only a
fan + a 4-hole inlet row — no exhaust. The old rect (Base Sketch6/Extrude6, symmetric
cut on wall midplane Y=79.3) was at the right X/width but wrong height (Z9.9-18.9),
so the holes (world Z~23.4, up near the sensor top) sat against solid wall.
Fix: deleted old Extrude6 (heals wall), redrew Sketch6 rect, rebuilt symmetric cut
-> INLET SLOT world X42-58 (16mm) × Z20.9-25.9 (5mm), centered X50/Z23.4. Covers
all 4 holes (world X44-56) + ~2mm seat play. Fan Ø22 @ X25 left untouched.
Verified via pointContainment: slot = through-opening (matches fan/USB signature),
old-exhaust spot now solid. BaseShell single healthy body.
Sketch6 coord map (for future edits): sketch x = world X (cm); sketch y = -(world Z);
plane Y=7.93cm (wall midplane); symmetric cut 3.45mm/side clears 2.6mm wall.
DONE: exported + printed + fit-confirmed 2026-07-03.

## ESP32-S3 dual-USB-C port — widened (2026-07-02)
Board = ESP32-S3-DevKitC-1-N32R16V: has TWO USB-C connectors on its end (native
USB + UART), but base had only ONE centered cutout (X120-132) -> one cable couldn't
seat. Espressif docs give board outline but NOT connector X-positions (image-only).
Estimated connector centers ~world X119 & X130.5 (pair centered on 25.5mm board end,
board spans X112-137.5). Chose robust fix: ONE wide "letterbox" slot spanning the
full board end -> Base Sketch7/Extrude7 rebuilt to world X113-137 (24mm) × Z5.4-12.4,
symmetric cut through back wall. Guaranteed to clear both connectors regardless of
exact placement; wall solid outside X113-137. Verified via pointContainment.
If you ever want two neat cutouts instead: measure the two port centers + widths.
DONE: exported + printed + fit-confirmed 2026-07-03.

## Sensor retention clips — added 2026-07-15
Goal: hold each gas sensor in its bay. No adhesive/magnets. Snap-on, removable.

MEASURED BAY FACTS (read off TrayBody, not guessed — reuse these):
- Bay floor top Z=4.9, wall top Z=10.9 (walls 6mm tall), wall_t 1.6.
- SCD40  inner X11.75-38.25 (26.5) Y9.10-32.90 (23.8); outer X10.15-39.85 Y7.50-34.50
- BME680 inner X49.30-75.70 (26.4) Y11.60-30.40 (18.8); outer X47.70-77.30 Y10.0-32.0
- SGP40  inner X83.50-109.90        Y11.60-30.40;        outer X81.90-111.50 Y10.0-32.0
- Wire channels cut FULL wall height (Z4.9-10.9), 8mm wide @ Y17-25, BOTH side
  walls -> each side wall is two 7.9mm stubs, not continuous. Connectors live
  there: nothing may press or grab the board's +/-X edges.
- Outboard free space (this decided the whole design):
  fronts >=7.25mm all 3 bays | SCD40 back only **1.5mm** (PMS cradle wall @ Y36.0)
  SCD40 sides 9.9 / 7.85mm | BME680 back 4.0mm | SGP40 back free
  **SGP40 right wall X109.90-111.50 is SHARED with the ESP cradle -> zero
  outboard space.** So no single arm orientation works for all 3 bays.

MECHANISM: through-WINDOW in bay wall + cantilever arm outside the wall with a
barb that snaps in. Window's TOP face IS the ledge (90 deg return). Chose window
over a protruding lip: nothing to shear off, prints with no overhang trick, and
each window adds bay airflow. Rejected simpler press-fit-only (would splay the
1.6mm walls) and floor-socket pegs (only 0.5mm/side of free floor).

ARM PLACEMENT (differs per bay by necessity, see free space above):
- Clip_SCD40: 4 arms on the +/-X walls @ Y12.25 / Y29.75 (clear of Y17-25 channels).
- Clip_Gas:   4 arms on the +/-Y walls @ X54.5 / X70.5 (BME680 local coords).

PARTS: Clip_SCD40 x1 (1785mm3). Clip_Gas x2 (1699mm3 ea) — BME680 and SGP40 bays
are dimensionally identical, so ONE part serves both; 2nd occurrence is the same
component shifted +34.2mm X. Print 2 of Clip_Gas.

CLIP GEOMETRY: ring plate Z14.0-15.6 w/ open centre (gas exchange); 4 legs down
to Z6.5 pressing PCB corners on 4x4mm pads; 4 arms t=1.4 w=6.0, 0.2mm clearance
to wall, barb 0.45mm deep.
TRAY CHANGE: 12 through-windows 6.0 x 1.8mm @ Z5.8-7.6. Removed 207.3mm3
(= 12 x 6 x 1.8 x 1.6 wall, exact). Wall left: 3.3mm above ledge, 0.9mm below.
Features named `SnapWindow_Tools` (BaseFeature) + `SnapWindows` (Combine/cut).
TrayBody still ONE healthy body.

VERIFIED (not assumed):
- All 12 windows probed via pointContainment: OPEN at Z6.7, SOLID at Z9.2
  (the ledge) and Z5.3. Controls pass.
- Clip-vs-tray boolean intersection = **0.000 mm3** all 3 clips.
- Snap arm strain: L~7.1 t=1.4 deflection 0.45 -> eps = 1.5*t*y/L^2 = **1.87%**,
  safe vs PETG ~4% allowable.
- Barb 6.4-7.4 in a 5.8-7.6 window -> ~0.2mm board play; tolerates PCB 1.5-1.7mm.

GOTCHA (cost a rebuild, do not re-derive): the barb's 45-deg lead-in ramp
descends 0.65mm BELOW the barb's full-protrusion band, and the barb starts
entering the wall 0.2mm up that ramp. First cut put the ramp toe at Z5.45, under
the window floor (5.8) -> ramp would jam on the wall below the window and the
clip could never seat. Showed up as a 1.470mm3 interference (= 4 x 0.3675, an
exact integral match, which is how it was localised). Fix: RAISED barb 0.5mm
(BARB_Z0 5.9 -> 6.4), did NOT thin the wall. Always assert interference==0
before committing clip bodies.

BUILD: script-built via Fusion MCP using TemporaryBRepManager boxes + booleans
(no sketches — avoids all sketch-plane orientation pain). Tunables live at the
top: PCB_T, PL_Z0/1, ARM_CLR/T/W, BARB_D, WIN_Z0/1, BARB_Z0/1.
Script was NOT saved to the repo (Jimmy declined) -> rebuild is manual.
MCP gotcha: BRepBody refs go STALE across BaseFeature.finishEdit(). Re-fetch
tool bodies BY NAME before Combine or it dies with ALL_TOOL_BODY_REFERENCE_LOST.

TODO before printing clips:
- [ ] **PCB_T=1.6 is ASSUMED** and sets the press height (LEG_Z = 4.9 + PCB_T
      = 6.5). Settle at the gas-sensor fit-test; if boards don't sit flat on the
      bay floor, this is the number to change.
- [ ] Confirm nothing tall sits at the board corners (legs press 4x4mm pads there).
- [ ] Print clips ON THEIR SIDE (rotate 90). Plate-down puts layer lines across
      the arm root = exactly where the bending stress goes.
- [ ] Save doc + export Clip_SCD40 / Clip_Gas STL/3MF (none exported yet).

## Hardware spec — closure (read off Sensor_Case geometry 2026-07-01)
- Inserts: **M3 brass heat-set**, set into the 4 BASE corner POSTS (Ø8 boss),
  one per corner. Pocket = Ø4.1 x 6mm deep BLIND hole in each post TOP face
  (Z 26.4..32.4); install from top (lid side). NOT in the lid.
- Insert part: standard M3 (~4.0mm OD, ~5.7mm long, e.g. CNC Kitchen M3x5.7 or
  generic M3x5/M3x4). Do NOT use extra-long M3 (would bottom out in 6mm pocket).
- Screws: **M3 socket-head cap, ~M3x8** (M3x10 ok for max engagement). Head
  recesses in lid Ø6.2 x 3mm counterbore; lid clears post top via Ø9.8 recess;
  ~5mm thread engages the insert. No separate Ø3.4 lid clearance hole (Ø9.8 recess
  serves that). 4 screws total, one per corner.

## Locked decisions
- Fusion MCP parametric; screw-down lid + M3 heat-set inserts (no gasket).
- Passive honeycomb venting; PMS isolated w/ round fan-intake port + exhaust.
- Hex honeycomb aesthetic; USB-C port, LED light-pipe, removable carrier tray.
- Material/print: PETG or ASA, 0.6mm nozzle, walls 2.6 / floor 2.4.
- Clearances (0.6mm nozzle): pocket +0.5, tray 0.25, lid lip 0.4, insert pilot 4.1.

## Fit-check revisions (2026-07-01, doc "Sensor_Case" — NOT the NoInserts doc)
Physical fit-check drove 3 direct-geometry edits (sketches are script-built, no
parametric dims, so param changes don't move geometry — edited geometry directly):
1. Tray ESP/SGP compartment overlap: shifted SGP pocket (Sketch7 + wire-notch
   Sketch8) -0.6mm X. SGP right wall now X[109.90..111.50] == ESP cradle left wall
   -> single SHARED 1.6mm wall (was 2.2mm interpenetration).
2. Tray hit the 4 corner POSTS (base screw bosses Ø8 / r=4 at (4,4)(4,74)
   (146,4)(146,74), full-height). The 1.85mm left-edge trim did NOT fix this
   (posts are at the corners, not along the edge) -> REVERTED that trim.
   Real fix: feature "CornerPostClearance" = 4 circular corner cutouts r=5.5
   (Ø11) centered on each boss. Clears Ø8 posts by 1.55mm radial; tray restored
   to full length 149.5mm (snug in 150 cavity -> stays located over the intake).
   Note: r=5.5 chosen because corner tip is 5.30mm from boss center; a smaller
   r (tried 4.6) isolates the tip as a <1mm3 sliver, and deleting slivers via
   body.deleteMe() also deletes the parent cut feature -> use r>=tip distance.
3. PMS intake was blocked ~1.5mm by tray floor: raised base intake circle
   (Sketch5) +2.0mm. Center world Z 14.4 -> 16.4; bottom 3.4 -> 5.4, clears tray
   floor top (4.89) by +0.51mm. Exhaust unaffected (bottom already ~9.9).
NOTE: Tray is a REMOVABLE carrier (separate print), NOT print-in-place.
TODO for Jimmy: SAVE the doc manually + RE-EXPORT tray.3mf/.stl and base.3mf/.stl
(current repo exports are pre-revision). Intake clearance is thin (0.51mm) — raise
more if you want margin, but that walks away from the real PMS fan center (still TBD).

## Open items / fit-check TBDs
- [x] PMS5003 port coords RESOLVED via datasheet (Downloads\pms5003.jpg). Vent
      face (50×21, against back wall) = fan Ø12 @ 35.80mm from left edge + 4 inlet
      holes (row, 6mm from left, 12mm span/4mm pitch, 2.45mm below top edge).
      Sensor seated hole/fan-end toward +X (ESP side): fan→world X~26 (under Ø22),
      holes→world X44-56 near top (world Z~23.4). See PMS inlet section below.
- [ ] LED light-pipe height (front wall X126, Z25mm) — align to actual ESP LED.
- [ ] USB-C cutout height/size vs actual connector.
- [ ] Board heights/pocket depths at physical test fit. NOTE this now also
      settles PCB_T for the retention clips (see clip section).
- [ ] PMS5003 + ESP32-S3 have NO retention clips — cradles only, different
      geometry. Revisit if they rattle.
- Honeycomb finalized: hex_af=5mm, rib=1.0mm, compact centered windows
  (front X35-115, left Y10-45, right Y33-68, Z band fl+5..fl+22). Base is now
  built by ONE consolidated script (shell+bosses+ports+LED+vents) -> easy to retune.

## Reusable notes / gotchas
- Fusion rejects param names 'wall' and 'floor' (reserved) -> used wall_t, floor_t.
- userParameter.value is in cm (internal units); positions built via mm()/10.
- Corner-notch circles can leave tiny disconnected slivers -> delete bodies <50mm3.
- Never save the Fusion document. Fusion MCP not thread-safe -> sequential calls only.
- MCP gotcha: Fusion MCP tools only load into a Claude Code session at startup;
  had to /reload-plugins (or restart session) after connecting the server.
