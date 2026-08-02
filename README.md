# TCP Flag Scanner

An interactive visualizer built for **Week 7a — "TCP: Raising the Wrong Flags"** on the [Goblin Eaters Cyber Cavern](https://ubritsa.com), part of the *How Protocols Work, and How They Break* series.

Live at: `ubritsa.com/tools/tcp-flag-scanner.html` *(once deployed via `gecc-publish`)*

---

## What it shows

Five TCP scan types, each demonstrated as two side-by-side scenarios so the contrast in behavior is visible in a single view rather than something you have to remember across separate runs:

| Scan | Row 1 | Row 2 | What it reveals |
|---|---|---|---|
| **SYN** | Target — port open | Target — port closed | The half-open handshake: real SYN, real SYN-ACK, connection abandoned before the final ACK. |
| **NULL** | Target — port open | Target — port closed | No flags set. Open ports stay silent; closed ports send RST — the RFC 793 quirk these scans exploit. |
| **FIN** | Target — port open | Target — port closed | FIN sent with no prior connection. Same silent/RST pattern as NULL. |
| **Xmas** | Target — port open | Target — port closed | URG + PSH + FIN set simultaneously. Same silent/RST pattern, different flag combination. |
| **ACK** | Direct — no firewall | Through a stateful firewall | Port state is irrelevant to this scan. What it actually reveals is whether a stateful device sits in the path — RST means unfiltered, silence means something dropped it before it arrived. |

## How to use it

- Click a scan type button to load that scenario into both rows.
- **Next / Prev** step through the exchange one beat at a time: probe sent → response received → synthesis.
- **Auto Play** runs the full sequence hands-free, pauses 2 seconds at the end, then loops back to the start automatically. Click it again (or Prev/Next/Reset) to stop.
- **Reset** clears the current scan back to its starting state without changing which scan is selected.

Each row shows:
- A **Sent** flag row and a **Received** flag row — six chips (URG, ACK, PSH, RST, SYN, FIN) in fixed order, filled green when set.
- A short **description** of what's happening at the current step, from the guard/portcullis perspective used in the article.
- A **result badge** (RST / Silent / Filtered / Unfiltered) that appears at the point in the diagram where that outcome actually occurs — at the target for most scans, at the firewall itself for the ACK scan's dropped case.

A synthesis line appears beneath both rows once the sequence completes, stating the comparison directly (e.g., "silence means staffed, rejection means it isn't" or "an ACK scan can't tell you open from closed").

## Design notes / limitations

- This tool intentionally shows **flags only** — no full frame/hex dissector like the ARP visualizer (`arp-visualizer-v2`). Flags are the entire mechanism this post is about; a full field-by-field breakdown would bury the one thing that matters.
- The **NULL/FIN/Xmas behavior shown is RFC 793-compliant behavior** (Unix/Linux-style stacks). Per the article: Windows, many Cisco devices, and IBM OS/400 do not follow this part of the RFC and will return RST regardless of port state — the tool does not currently model that non-compliant case as a toggle.
- The **firewall in the ACK scan row is a stateful firewall** specifically. Stateless firewall/no-firewall behavior is represented by the "direct" row (identical outcome to no firewall at all), not as a separate third state.
- Row heights are fixed (260px) regardless of scan selected, so the layout doesn't shift size as you click between scan types.

## Tech

Single self-contained HTML file — no build step, no external dependencies beyond Google Fonts (JetBrains Mono, Syne). Canvas-based mini visualizations per row, vanilla JS state machine for steps/auto-play. Font sizing uses `rem`/`clamp()` throughout, no hard-coded pixel font sizes.

## Related

- Article: Week 7a, "TCP: Raising the Wrong Flags"
- Series plan: `GoblinEater_Protocol_Series_Plan_v2.docx`
- Sibling tool: `arp-visualizer-v2` (Week 2, ARP request/reply and poisoning)
- Next up: Week 7b — stateful vs. stateless firewall decision visualizer, expanding on the single pass/drop behavior shown here

---

Goblin Eaters Cyber Cavern · ubritsa.com
