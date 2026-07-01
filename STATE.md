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

## Locked decisions
- Fusion MCP parametric; screw-down lid + M3 heat-set inserts (no gasket).
- Passive honeycomb venting; PMS isolated w/ round fan-intake port + exhaust.
- Hex honeycomb aesthetic; USB-C port, LED light-pipe, removable carrier tray.
- Material/print: PETG or ASA, 0.6mm nozzle, walls 2.6 / floor 2.4.
- Clearances (0.6mm nozzle): pocket +0.5, tray 0.25, lid lip 0.4, insert pilot 4.1.

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
