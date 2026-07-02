# STATE — Sensor Stack Enclosure

**Project:** Sensor Enclosure (JDP3D) | **Owner:** Jimmy Pezzone
**Active track:** enclosure-v1
**Last updated:** 2026-07-01

## Current state
v1 model COMPLETE in Fusion (Untitled doc — NOT saved; Jimmy saves manually).
3 components built + exported. Awaiting Jimmy's review + physical fit-check.
Full plan: `C:\Users\jdp63\.claude\plans\i-have-a-sensor-quizzical-owl.md`

## Model summary (Fusion, 49 user params driving geometry)
- External size: **155.2 × 83.2 × ~39.4 mm** (base 32.4 tall + lid 7).
- Interior: 150 × 78 × 30 mm.
- **Base** (1 body): filleted shell, chamfered floating base, 4 corner insert
  bosses (Ø8, 4.1mm pilot for M3 heat-set), back-wall ports (PMS round intake +
  rect exhaust + USB-C), honeycomb vents front + both sides, LED light-pipe port.
- **Tray** (1 body): floor 2.5mm + corner boss notches, 3 gas-sensor pockets
  (wire notches), PMS cradle (open back → intake), ESP cradle (open back → USB),
  2 front finger scallops. 0.25mm/side clearance to cavity.
- **Lid** (1 body): 3mm plate, matching fillet + top chamfer, register lip (0.4mm
  clr, corner-notched), 4 M3 screw counterbores aligned to inserts.

## Board layout (mm, interior min-corner x0,y0)
scd(12,12) bme(50,12) sgp(85,12) | pms(12,38) esp(112,6). Corner bosses (4,4)(146,4)(4,74)(146,74).
Thermal zoning: hot back (PMS+ESP) vs cool front (gas sensors); SCD40 front-left, farthest from ESP.

## Exports (E:\jdp3d\sensor enclosure\export\)
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
- [ ] PMS5003 intake/exhaust EXACT port coords (datasheet is image-only) —
      currently intake X=25 exhaust X=50 within PMS, cz=12mm. Verify vs real unit.
- [ ] LED light-pipe height (front wall X126, Z25mm) — align to actual ESP LED.
- [ ] USB-C cutout height/size vs actual connector.
- [ ] Board heights/pocket depths at physical test fit.
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
