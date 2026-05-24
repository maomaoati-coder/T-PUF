<div align="center">

<img src="https://img.shields.io/badge/Status-Preprint%20%7C%20Open%20for%20Review-0a1e45?style=for-the-badge&labelColor=1a3a6e" />
<img src="https://img.shields.io/badge/DOI-10.5281%2Fzenodo.20353599-blue?style=for-the-badge&logo=zenodo&logoColor=white" />
<img src="https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey?style=for-the-badge" />
<img src="https://img.shields.io/badge/Standard-PLC--STD--001-0a1e45?style=for-the-badge" />

<br /><br />

```
╔══════════════════════════════════════════════════════════════════╗
║                                                                  ║
║        T - P U F                                                 ║
║        Temporal Physical Unclonable Function                     ║
║                                                                  ║
║        Security that does not hide a secret.                     ║
║        Security grounded in the arrow of time itself.            ║
║                                                                  ║
╚══════════════════════════════════════════════════════════════════╝
```

# Temporal Physical Unclonable Function (T-PUF)

### *A Time-Irreversible Hardware Security Architecture Beyond Mathematical Hardness Assumptions*

**Guanghui Mao** — Independent Chip Architect  
SiliconForge Independent Research · `maomaoati-coder`

[📄 Full Whitepaper (CN)](https://zenodo.org/records/20353599) · [📄 Abstract (EN)](https://zenodo.org/records/20353599) · [🔗 DOI: 10.5281/zenodo.20353599](https://doi.org/10.5281/zenodo.20353599)

</div>

---

## The Problem with Every Cryptosystem That Exists Today

Every cryptographic system ever deployed — RSA, ECC, AES, blockchain — shares one fatal structural flaw:

> **They secure a secret by hiding it.**

A private key is a string of bits. Bits are perfectly copyable. The moment an adversary obtains a copy, the entire security model collapses — not gradually, but completely and irreversibly.

Nature operates differently.

Two leaves grown from the same branch at the same moment share identical DNA. Yet every microscopic vein, every boundary curve is unrepeatable — because the physical process of growth was governed by thermodynamic noise unique to that instant in time and space.

**T-PUF internalises this principle.** Security is not achieved by hiding a secret. Security is achieved by the physical impossibility of reversing time.

---

## Core Insight

```
commit(t₀) = Hash( f_HW(C) ‖ E(t₀) ‖ t₀ )
```

| Symbol | Meaning |
|--------|---------|
| `t₀` | The generation moment — an unrepeatable point in physical time |
| `E(t₀)` | Instantaneous entropy snapshot at `t₀` — consumed upon sampling, permanently gone |
| `f_HW` | Hardware fingerprint function — determined by transistor-level manufacturing variation |
| `commit` | The public output — safe to publish openly; useless to anyone who cannot travel to `t₀` |

The entropy `E(t₀)` is **never stored**. It ceases to exist the moment the commitment is produced.  
The commitment itself can be broadcast to the entire world. It grants zero advantage to any adversary.

---

## Three Physical Axioms

The security of T-PUF rests on physical law, not computational assumption.

```
Axiom I   — Time Irreversibility
            ∀ t₁ > t₀ : E(t₁) ≠ E(t₀)
            The microscopic thermodynamic state of any physical device
            evolves monotonically. No process restores a prior state.

Axiom II  — Entropy Sampling Inseparability
            Any observation of E(t₀) injects energy that alters
            the state being observed. Sampling is consumption.
            Lossless copying does not exist.

Axiom III — Timestamp Unforgeability
            commit(t₀) is anchored to the universe's time axis via
            physical multi-party witnessing, preventing substitution
            of any later state for the original t₀.
```

**Security proof (sketch):**

```
Breaking T-PUF identity
  ⟺  Reconstructing E(t₀) from commit(t₀)
  ⟺  Reversing the thermodynamic entropy increase since t₀
  ⟺  Violating the Second Law of Thermodynamics
  ⟺  Physically impossible
```

This holds regardless of the adversary's computational resources. Unbounded classical compute, quantum computers, future algorithms — none of these reverse thermodynamics.

---

## Architecture

### Hybrid Security Model

T-PUF separates two orthogonal security properties:

```
┌─────────────────────────────────────────────────────────────┐
│  LAYER A — Identity Uniqueness & Unclonability              │
│  Foundation: Thermodynamic law (unconditional)              │
│  Threat: Physical cloning → impossible by Axioms I–III      │
├─────────────────────────────────────────────────────────────┤
│  LAYER B — Zero-Knowledge Public Verifiability              │
│  Foundation: Post-quantum ZK-STARKs (lattice-based)         │
│  Threat: Forged proofs → computationally infeasible         │
└─────────────────────────────────────────────────────────────┘
```

Even if Layer B were broken (a forged ZK proof), Layer A remains intact. The adversary still cannot clone the physical device or reconstruct `E(t₀)`. The two layers address orthogonal attack surfaces.

---

### Multi-Source Entropy Fusion

Within the critical **100 ms cold-boot window**:

```
                    [ T-PUF Entropy Sources ]
                             │
        ┌────────────────────┼────────────────────┐
        ▼                    ▼                    ▼
  ┌──────────────┐   ┌──────────────┐   ┌──────────────┐
  │   PRIMARY    │   │  SECONDARY   │   │   AMBIENT    │
  │              │   │              │   │              │
  │ SRAM start-up│   │ CMOS dark-   │   │  Gyroscope   │
  │ bitmap       │   │ frame scatter│   │  micro-jitter│
  │              │   │              │   │              │
  │ Fab variation│   │ Thermal+light│   │  Spatial     │
  │ (spatial)    │   │ coupling     │   │  geometry    │
  └──────┬───────┘   └──────┬───────┘   └──────┬───────┘
         └──────────────────┼──────────────────┘
                            ▼
                     [ BLAKE3 Hash ]
                            ▼
                       commit(t₀)
```

SRAM start-up noise is the primary source because it simultaneously satisfies all three axioms: manufacturing variation (spatial uniqueness), power-on voltage transient (temporal uniqueness), and electromigration drift over device lifetime (time irreversibility).

---

### Delayed Zero-Knowledge Verification Protocol

```
Step 1 │ Commit (t₀)      │ Publish commit(t₀) openly.
       │                   │ Physically zero E(t₀) immediately.
───────┼───────────────────┼────────────────────────────────────────
Step 2 │ Challenge (t₁)   │ Verifier issues random challenge C
       │                   │ at any future time t₁.
───────┼───────────────────┼────────────────────────────────────────
Step 3 │ Response (t₁)    │ Device produces R = Ψ(C) using its
       │                   │ resident hardware fingerprint.
       │                   │ No reconstruction of E(t₀) required.
───────┼───────────────────┼────────────────────────────────────────
Step 4 │ ZK Proof (t₁)    │ Local ZK-STARKs engine generates π:
       │                   │ "The hardware producing R is the same
       │                   │  physical entity that produced commit
       │                   │  at t₀."
───────┼───────────────────┼────────────────────────────────────────
Step 5 │ Verify            │ Any party verifies (commit, C, R, π)
       │                   │ without accessing device or E(t₀).
```

---

### Passive Self-Destruct Honeypot

```
Intrusion Event Detected
        │
        ├─► Level 1 (soft)   Debugger / illegal register access
        │                    → Freeze commit chain · R permanently invalidated
        │
        ├─► Level 2 (hard)   Capacitance spike / JTAG probe / delamination
        │                    → Active SRAM discharge · fingerprint overwritten
        │
        └─► Level 3 (nuke)   Repeated unauthorised verification attempts
                             → Full local identity self-destruct · open-circuit
```

On Level 1/2 trigger: the system returns a thermally-seeded **pseudo-response** that appears plausible — luring the adversary deeper while silently logging their behaviour as legally admissible forensic evidence.

> **Legal boundary:** All countermeasures are purely passive and contained within the owner's device. No outbound access to any third-party system occurs.

---

### Sovereign Hardware Stack (PLC-STD-001)

```
┌────────────────────────────────────────────────────────────────┐
│  Layer 3  Application API     ZK-STARKs proof engine           │
├────────────────────────────────────────────────────────────────┤
│  Layer 2  Commitment layer    BLAKE3 hardware hash unit        │
├────────────────────────────────────────────────────────────────┤
│  Layer 1  Entropy sampler     SRAM + CMOS + IMU (100ms window) │
├────────────────────────────────────────────────────────────────┤
│  Layer 0  Physical PUF core   Transistor mismatch fingerprint  │
│                               No software access · No JTAG     │
└────────────────────────────────────────────────────────────────┘
```

**3D-stacked integration mapping:**

```
  chip3d_security_top  ──►  Layer 3 + Layer 2  (ZKP & hash engines)
           ▲
     tsv_channel        ──►  Dedicated entropy transport via TSV
           ▼
  sovereign_base        ──►  Layer 1 + Layer 0  (SRAM cells + sensors)
           ▲
  m2_intercept          ──►  Self-destruct discharge gates
```

---

## Comparison

| Dimension | RSA / AES | Blockchain | SRAM PUF | **T-PUF** |
|-----------|-----------|------------|----------|-----------|
| Security root | Math hardness | Consensus + math | Spatial uniqueness | **Thermodynamic law** |
| Key storage | Required | Required | Zero (dynamic) | **Zero (entropy destroyed)** |
| Public commitment | Fatal if leaked | Fatal if leaked | Not possible | **Safe to publish openly** |
| Quantum resistance | Broken (Shor) | Broken | Inherent | **Absolute (physics)** |
| Physical relocation | Trivially copied | Trivially dumped | Partial risk | **Immune — t₀ has passed** |
| Defence posture | Passive | Passive | Static | **Active self-destruct honeypot** |

---

## Open Research Problems

| ID | Problem | Direction |
|----|---------|-----------|
| **P1** | ZK proof latency | Optimise ZK-STARKs on mobile SoCs to complete within 100ms at acceptable thermal cost |
| **P2** | Entropy source ageing | Design fuzzy extractor to maintain commit alignment over 5-year device lifetime |
| **P3** | Decentralised timestamp | Joint anchoring via decentralised time chain + radioactive decay pulse events |
| **P4** | Sub-micron probe countermeasures | Model m2_intercept discharge rate to guarantee self-destruct before first charge extraction |

---

## Repository Structure

```
T-PUF/
├── docs/
│   ├── T-PUF_Whitepaper_CN_v1.0.pdf     # Full technical whitepaper (Chinese)
│   └── T-PUF_Abstract_EN_v1.0.pdf        # Abstract & architecture (English)
├── standard/
│   └── PLC-STD-001.md                    # Technical standard specification
└── README.md
```

---

## Citation

```bibtex
@misc{mao2026tpuf,
  title     = {Temporal Physical Unclonable Function (T-PUF):
               A Time-Irreversible Hardware Security Architecture
               Beyond Mathematical Hardness Assumptions},
  author    = {Mao, Guanghui},
  year      = {2026},
  month     = {05},
  publisher = {Zenodo},
  doi       = {10.5281/zenodo.20353599},
  url       = {https://doi.org/10.5281/zenodo.20353599},
  note      = {Preprint · PLC-STD-001 · SiliconForge Independent Research}
}
```

---

## License

Documentation and whitepaper: [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/)

Hardware implementation specifications and RTL: Proprietary — All Rights Reserved  
© 2026 Guanghui Mao / SiliconForge Independent Research

---

<div align="center">

*"Give it to anyone. Anyone who receives it still cannot use it.*  
*Because the moment of its creation has already passed."*

**SiliconForge Independent Research**  
`maomaoati-coder` · [Zenodo Records](https://zenodo.org/records/20353599)

</div>
