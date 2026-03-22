# EntraID Identity Attack Model

A single-file interactive threat modelling tool for Microsoft Entra ID (Azure AD). Build, visualise, simulate, and mitigate modern OAuth and identity attack paths — no backend, no install, runs entirely in the browser.

---

## Quick Start

1. Open `entra-attack-model.html` in any modern browser.
2. A **Full Kill Chain** template loads automatically as a starting point.
3. Use the toolbar templates or sidebar to build your own model.
4. Switch to **Graph View** for a force-directed network layout.
5. Run a simulation from the **Simulation** tab in the right panel.

---

## Interface Overview

```
┌─────────────────────────────────────────────────────────────────┐
│  TopBar  │ EntraID Attack Model │ ⊞Canvas ◉Graph │ Import Export │
├──────────┬──────────────────────────────────────┬───────────────┤
│          │  Toolbar (templates + legend)        │               │
│ Sidebar  │                                      │  Right Panel  │
│          │  Canvas / Graph View                 │  ─────────    │
│ Attack   │                                      │  Node Props   │
│ Paths    │  (main working area)                 │  Simulation   │
│ Post-    │                                      │  Mitigations  │
│ Comprom. │                                      │               │
│ Targets  ├──────────────────────────────────────┤               │
│ Defenses │  Status Bar (node/edge count, sim)   │               │
└──────────┴──────────────────────────────────────┴───────────────┘
```

---

## Views

### ⊞ Canvas View (default)
Freeform node-and-edge diagram on a dot-grid canvas.

- **Drag** nodes to reposition them.
- **Shift + click** two nodes to draw a directed edge — a dialog prompts for a label and edge type.
- **Click** a node to select it and open its properties in the right panel.
- **Double-click** a node to focus the properties panel.

### ◉ Graph View
Dark-themed force-directed network powered by a custom physics engine.

- **Drag** individual nodes to reposition them.
- **Drag** the canvas background to pan.
- **Scroll** to zoom in/out.
- **⟳ Force Layout** button runs an iterative spring simulation to automatically arrange nodes.
- **Hover** over a node to see a tooltip with full details, MITRE technique, and notes.
- All connections are drawn as curves with colour-coded arrows matching edge type.

---

## Building a Model

### Adding Nodes

Click any item in the left sidebar to drop a node onto the canvas:

| Section | Node Types |
|---|---|
| **Attack Paths** | Phishing, Device Code, AiTM, OAuth Consent, Password Spray, Token Theft |
| **Post-Compromise** | BEC / Mail Rules, Privilege Escalation, App Reg. Backdoor, Lateral Movement, Data Exfiltration |
| **Targets / Assets** | User Identity, Global Admin, App Registration, Exchange Mailbox, SharePoint / Teams |
| **Defenses** | MFA (FIDO2/TOTP), Conditional Access, Defender for Identity, Honeypot / Decoy |

### Pre-built Templates (Toolbar)

| Button | What it builds |
|---|---|
| **+ Device Code** | Device code phishing chain with MFA check node |
| **+ AiTM Chain** | Phishing → AiTM proxy → BEC → mailbox with Defender detection |
| **+ BEC Chain** | Full BEC scenario with exfiltration and lateral movement |
| **+ Full Kill Chain** | 15-node end-to-end attack including all phases and defences |

> Loading a template clears the current canvas.

### Connecting Nodes

1. Hold **Shift** and click the **source** node — a toast confirms selection.
2. Hold **Shift** and click the **target** node.
3. A dialog opens: enter an optional label and choose the edge type.

### Edge Types

| Type | Colour | Meaning |
|---|---|---|
| Attack Path | 🔴 Red | Offensive lateral movement or exploitation |
| Mitigation / Block | 🟢 Green | Control that stops or impedes the path |
| Detection / Alert | 🔵 Blue | Monitoring or alerting capability |
| Decoy Trigger | 🟡 Yellow dashed | Honeypot interaction triggers an alert |

### Node Properties (Right Panel → Node tab)

Select any node to edit:
- **Label** — display name shown on the node
- **Badge** — category tag (e.g. "Initial Access", "Target")
- **MITRE Technique** — free-text field, displayed in graph view tooltips
- **Notes** — contextual detail shown in graph tooltips
- **Decoy toggle** — marks node with a 🍯 honeypot indicator
- **Mitigated toggle** — marks node with a ✓ green badge

---

## Simulation

Open the **Simulation** tab in the right panel.

### Controls

| Control | Description |
|---|---|
| **Attack Scenario** | Choose from 6 pre-built scenario scripts |
| **Speed** | Fast / Normal / Slow — controls log step interval |
| **Phishing-Resistant MFA** | Toggle on to block AiTM in simulation |
| **Conditional Access** | Toggle on to block Device Code and token replay paths |
| **Defender for Identity** | Toggle off to suppress alert log lines |
| **Trigger Decoys** | Toggle on to include honeypot alert events |

### Scenarios

| Scenario | Covers |
|---|---|
| **Device Code Phishing** | RFC 8628 abuse, no-password OAuth token issuance |
| **AiTM (Adversary-in-the-Middle)** | Evilginx/EvilProxy session cookie theft bypassing MFA |
| **Business Email Compromise** | Post-AiTM inbox rule deployment, wire fraud |
| **Illicit OAuth Consent** | Malicious app consent, persistent Graph API access |
| **Token Theft & Replay** | PRT exfiltration, replay without device binding |
| **Full Kill Chain** | All phases: initial access → escalation → BEC → exfil |

### Running a Simulation

1. Select a scenario and configure toggles.
2. Click **▶ Run** — log lines stream into the Simulation Log panel.
3. Green lines = blocked/detected. Red lines = attacker success.
4. Click **↺ Reset** to clear the log and start again.

---

## Mitigations

Click any item in the **Mitigations** tab to apply it. Applied mitigations mark relevant defence nodes with a green ✓ badge and affect simulation outcomes when the corresponding toggle is also enabled.

| Mitigation | Blocks |
|---|---|
| Phishing-Resistant MFA (FIDO2/Passkeys) | AiTM, Device Code flow |
| Block Device Code Flow (CA policy) | Device Code phishing |
| Token Protection (Preview) | PRT / token replay attacks |
| Require Compliant Device | Unmanaged device token use |
| PIM for Admin Roles | Privilege escalation blast radius |
| App Consent Policies | Illicit OAuth consent grants |
| Defender XDR | BEC detection, impossible travel |

---

## Decoys & Deception

Add a **🍯 Honeypot / Decoy** node from the sidebar. Honeypot nodes:
- Render with a dashed yellow border and 🍯 badge in Canvas view.
- Appear as glowing yellow circles in Graph view.
- Generate `[DECOY]` alert lines in simulation log when "Trigger decoys" is enabled.
- Represent fake high-value accounts, service principals, or named pipes that fire Defender alerts on first access.

Connect a decoy to an attack path using a **Decoy Trigger** edge to model the detection flow.

---

## Import / Export

### Export
Click **⬇ Export** in the top bar. Downloads `entra-attack-model.json` containing all nodes and edges.

### Import
Click **⬆ Import**, paste a previously exported JSON object, and click **Import**.

### JSON Format

```json
{
  "nodes": [
    {
      "id": "n1",
      "title": "Device Code Phishing",
      "icon": "📱",
      "color": "#e65100",
      "iconBg": "#fff3e0",
      "badge": "OAuth Abuse",
      "badgeClass": "badge-orange",
      "type": "attack",
      "notes": "Attacker generates device code...",
      "tactic": "Credential Access",
      "technique": "T1528",
      "x": 200,
      "y": 150,
      "isDecoy": false,
      "isMitigated": false
    }
  ],
  "edges": [
    {
      "id": "e1",
      "from": "n1",
      "to": "n2",
      "label": "Token Issued",
      "type": "attack"
    }
  ]
}
```

Node `type` values: `attack`, `post`, `target`, `defense`, `decoy`
Edge `type` values: `attack`, `mitigation`, `detection`, `decoy`

---

## Keyboard & Mouse Reference

| Action | How |
|---|---|
| Add node | Click sidebar item |
| Move node (canvas) | Drag |
| Draw edge | Shift + click source, Shift + click target |
| Select node | Click |
| Pan graph | Drag background (Graph view) |
| Zoom graph | Scroll wheel (Graph view) |
| Auto-layout | ⟳ button (Graph view) |
| Node tooltip | Hover (Graph view) |

---

## Attack Technique References

| Technique | MITRE ID |
|---|---|
| Device Code Authentication Abuse | T1528 |
| Adversary-in-the-Middle | T1557 |
| Phishing | T1566 |
| OAuth Application Token Theft | T1550.001 |
| Password Spraying | T1110.003 |
| Email Collection / Forwarding | T1114.003 |
| Privilege Escalation via Cloud Accounts | T1078.004 |
| Account Manipulation (App Credentials) | T1098.001 |
| Data Exfiltration via Web Service | T1567.004 |
| Use of Alternate Authentication Material | T1550 |

---

## Requirements

- Any modern browser (Chrome, Edge, Firefox, Safari)
- No installation, no dependencies, no internet connection required after initial load
- Google Fonts loaded from CDN on first open (gracefully degrades offline)
