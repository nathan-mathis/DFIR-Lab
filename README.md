# DFIR Lab - Digital Forensics and Incident Response

**Active Build - Work in Progress**

**A structured incident response lab built for repeatable IR cycles - practicing decisions, not tools. One incident type, run end-to-end, until the steps feel boring and the confidence shows up on its own.**

Built by [Nathan Mathis](https://www.linkedin.com/in/n-mathis/) | IT Professional | 14+ Years Enterprise IT Experience

---

## Philosophy

> Never practice tools. Practice decisions.

Tools are just the interface. Every exercise must answer:

- What do I collect?
- What do I NOT touch?
- What can I safely conclude?
- What remains uncertain?

Confidence doesn't come from knowing more tools or watching more videos. It comes from one moment: "I've seen this shape of problem before." That only happens after complete cycles - not partial labs, not tutorials. Full cycles.

This lab follows the surgical training model: same incision, different patient. One incident type repeated until the workflow is muscle memory.

---

## Lab Environment

| Component | Role | Details |
|-----------|------|---------|
| **Windows 10 VM** | Victim | Target endpoint for simulated incidents |
| **Kali Linux VM** | Attacker | Payload preparation and delivery |
| **Ubuntu VM** | Analyst | Evidence collection, analysis, and reporting |
| **Host Machine** | Hypervisor | AMD Ryzen 7 7800X3D, 32GB RAM, NVMe Storage |
| **Hypervisor** | Platform | VMware Workstation |

All VMs operate on a host-only network. No internet exposure during incidents.

---

## The Workflow - 7 Phases

The core incident scenario: **"Suspicious execution on a corporate endpoint."**

Same scenario. Every time. Different execution details each cycle.

### Phase 1 - Attack Preparation (Kali)

Build a benign suspicious binary on the attacker VM. Transfer to victim via HTTP server. The payload is not malicious - it simulates suspicious behavior (process execution, file creation, network connection) to generate artifacts for investigation.

**Key constraint:** Do not execute on victim yet.

### Phase 2A - Victim Pre-Execution Prep (Windows)

Before detonation:

- Seed test documents in user directories (ensures copy artifacts exist)
- Stage WinPmem memory acquisition tool via ISO mount to `C:\IR_Tools\`
- Confirm Windows audit logging:
  - Event ID 4688 (Process Creation) - enabled via `secpol.msc`
  - Command line logging - enabled via `gpedit.msc`
  - Verified by opening Notepad and checking for 4688 event
- Confirm Windows Defender is active
- **Snapshot the VM** (pre-execution baseline)

### Phase 2B - Analyst Pre-Execution Prep (Ubuntu)

- Create case folder structure:

```
Labs/Case_001/
  Evidence/
    Memory/
    Disk/
  Logs/
  Notes/
```

- Configure SMB share on host-only network for evidence transfer
- Test connectivity from victim to analyst share
- Create `Acquisition_Log.txt` with case metadata

### Phase 3 - Execution (Windows)

Execute the payload. Do not interact with the system. Wait 1-2 minutes for artifacts to generate.

### Phase 4 - Memory Acquisition (Critical Timing)

The most time-sensitive phase. Volatile evidence disappears at shutdown.

**Acquisition timeline:**
1. Execute WinPmem from `C:\IR_Tools\` - capture physical memory to raw file
2. Verify file exists and size is reasonable relative to RAM
3. Generate SHA256 hash on victim (integrity baseline)
4. Transfer evidence to analyst share via SMB
5. Verify hash on analyst machine - **must match exactly**
6. Document everything: case ID, hostname, RAM size, tool version, command used, file path, file size, hash, timestamp, responder
7. Controlled shutdown of victim VM only after hash verification

**If hash mismatch: stop workflow. Do not proceed.**

### Phase 5 - Disk Acquisition *(Not Yet Started)*

Shut down victim VM. Acquire full disk image from analyst workstation. Store with case evidence.

### Phase 6 - Analysis Plan *(Outlined, Not Yet Executed)*

Planned analysis order:
1. Timeline from $MFT
2. Prefetch
3. Amcache
4. Shimcache
5. Security Log (Event ID 4688)
6. SRUM
7. Memory analysis: pslist, netscan, cmdline, handles
8. Build unified timeline
9. Write report

### Phase 7 - Reporting *(Not Yet Authored)*

One-page report structure:
1. **Executive Summary** - What happened, how you know, current risk
2. **Confirmed Findings** - Only facts you can prove
3. **Unconfirmed / Unknowns** - What you cannot prove, gaps in evidence
4. **Recommended Next Actions** - Containment, further investigation, monitoring

---

## Week-1 Training Structure

The first week follows a structured daily progression:

| Day | Focus | Constraint |
|-----|-------|------------|
| Day 0 | Lab setup, folder structure, rules | One-time setup |
| Day 1 | Create incident, triage, volatile data, disk preservation | No analysis yet |
| Day 2 | Artifact extraction, timeline construction, report v1 | Collect, don't interpret |
| Day 3 | Reset and repeat with one variable changed | Same checklist |
| Day 4 | Memory-first analysis | **No disk allowed** |
| Day 5 | Memory-based network and code context | Still no disk |
| Day 6 | Reconciliation: memory vs disk | Compare findings |
| Day 7 | Unified timeline, report v2 | Memory + disk combined |

**The Day 4-5 constraint is intentional.** Forcing memory-only analysis teaches what volatile evidence can reveal that disk never captures - and builds the skill of working with incomplete information.

---

## Rules (Non-Negotiable)

- Snapshots before every incident
- No internet during incidents
- Never "poke around" casually
- Everything gets written down
- If you violate a rule, roll back and start over
- Documentation must not occur on the victim system
- Never write to the original evidence

**That discipline is the training.**

---

## Current Build Status

| Phase | Status |
|-------|--------|
| Phase 1 - Attack Preparation | Documented |
| Phase 2A - Victim Prep | Documented |
| Phase 2B - Analyst Prep | Documented |
| Phase 3 - Execution | Documented |
| Phase 4 - Memory Acquisition | Documented |
| Phase 5 - Disk Acquisition | Not started |
| Phase 6 - Analysis | Outlined |
| Phase 7 - Reporting | Not authored |
| Week-1 Checklist | Complete |

---

## How This Connects to My Other Labs

This lab doesn't exist in isolation:

- **[Active Directory Lab](https://github.com/nathan-mathis/LABCORP-ActiveDirectory)** - Provides the domain environment where attack and response scenarios take place
- **[Data Recovery Lab](https://github.com/nathan-mathis/Data-Recovery-Lab)** - The forensic recovery of a 14-year-old hard drive is what sparked this entire DFIR path. The same preservation principles (image before touching, verify hashes, never modify the source) carry directly into incident response
- **[Help Desk Simulation](https://github.com/nathan-mathis/HelpDesk-Simulation)** - The documentation discipline (DAS framework) translates into IR reporting

---

## Key Skills Being Developed

- Incident response workflow and sequencing
- Volatile evidence preservation (memory acquisition)
- Evidence integrity verification (SHA256 hashing)
- Windows audit log configuration (Event ID 4688, command line logging)
- SMB-based evidence transfer in isolated networks
- Case folder structure and IR documentation
- Memory-first analysis methodology
- Timeline construction from multiple evidence sources
- Forensic imaging and chain of custody
- Report writing under uncertainty

---

## What's Next

- [ ] Complete Phase 5 - disk acquisition workflow
- [ ] Author Phase 7 - reporting template
- [ ] Execute first full end-to-end IR cycle
- [ ] Run 10+ cycles to build muscle memory
- [ ] Integrate SIEM for centralized log monitoring
- [ ] Practice with CTF forensics challenges
- [ ] Work toward relevant certifications (CompTIA Security+, GIAC GCFE)

---

## About Me

IT professional with 14+ years of enterprise experience in IMACD (Install, Move, Add, Change, Dispose) service delivery at Dell Technologies supporting Boeing facilities. CompTIA A+ certified, currently pursuing Network+ (N10-009), with a long-term career path toward Digital Forensics and Incident Response (DFIR).

The DFIR interest started with a data recovery project - pulling files off a 14-year-old hard drive using forensic techniques. That experience shifted my perspective from "IT support" to "evidence preservation" and opened a door I'm still walking through.

- [LinkedIn](https://www.linkedin.com/in/n-mathis/)
- [Resume available upon request]
