# Proposals

Fresh experiment started 2026-10-19 ($1,000 paper bankroll). Prior proposals: archive/journal-2026-10-19/proposals.md

## P1 (2026-10-18 16:19Z, FULL cycle, operator machine): gamma-api returns HTTP 451 to this machine; scan finds 0 candidates without saying why

**Symptom.** The first FULL cycle after the restart (lease taken by loop.sh at 15:57:59Z)
ran `core/scan.py --hours 336 --limit 800`. All 6 discovery queries (liquid-multiday,
by-liquidity, active-today, econ-tag, mentions-tag, commodities-tag) failed after 3 tries with
`HTTP Error 451: Unavailable For Legal Reasons` from `gamma-api.polymarket.com/markets`.
scan printed `0 candidates total`. `screen.py prepare` printed `no valid candidates on input`.
No market could be screened, researched, priced or placed.

**Why it is not mine to fix.** 451 is the venue refusing this network location. It is the
same kind of event as REAL.md's "403/geoblock is venue policy: report it and stop". I did not
try a different egress (WebFetch, proxy) and will not.

**Asks (operator):**
1. Check this machine's egress (VPN, region, ISP change since the last operator-machine cycle).
   The last operator-machine cycle in the archived cycles.log is 2026-10-07T09:17Z, and it
   scanned normally. Cloud cycles kept scanning normally through 2026-10-18T14:25Z (14:12Z
   FULL screened 291 rows). So the block is new on this machine since 10-07, or it depends on the network the
   machine is on now.
2. `core/scan.py`: when every query fails with the same HTTP status, exit non-zero and print
   one summary line (e.g. `scan: ALL queries failed: HTTP 451 (venue geoblock?)`).
   Today the output is "0 candidates", which a cycle can read as an empty market.
3. `loop.sh`: consider a gamma reachability probe next to the Pearl health check. If gamma
   returns 451, the tick could run LIGHT on Sonnet instead of spending an Opus FULL that
   cannot scan. Settlement (`resolve.py`) also reads gamma, so a long block on this machine
   would also stall settlement here. The cloud runner is unaffected as far as I can tell.
4. If the block persists, real-mode cycles on this machine cannot place orders either
   (core/real.py needs the same venue). Worth confirming before the next `--real` run.
