# lab-workflow-and-methodology

**Author:** Sammatha Adams  
**Series:** 5-Lab IT Portfolio Series  
**Stack:** Azure · Windows Server · PowerShell · VS Code · GitHub

---

## What This Repo Is

This repo documents the personal workflow, tooling setup, and learning philosophy I apply across each lab in my 5-lab IT portfolio series. It captures not just *what* I do in each lab, but *how* and *why* — so the methodology itself is part of the portfolio and reflects the kind of structured, professional thinking expected in a sysadmin or cloud admin role.

---

## Project Instructions

Every lab in the series is built against a consistent set of project-level requirements:

- Each lab produces a **README.md** with business-context framing and embedded screenshots at each significant step
- Each lab produces an **SOP/runbook** (.txt) with step-by-step instructions and inline explanations of every command
- All **scripts** live in a `/scripts` subdirectory and are tracked in Git from the first commit
- **Architecture diagrams** are produced as SVG files with a consistent visual style across all five labs
- The README reflects how the work would be documented in an enterprise environment — not just as a how-to, but as a record of what was built, why, and what it demonstrates

---

## Workstation Setup

### Multi-Monitor Configuration

Labs are completed across two screens:

| Screen | Purpose |
|---|---|
| Primary | VS Code — terminal, script editor, Git workflow |
| Secondary | Azure portal, RDP session into the Windows Server VM, or the lab document |

Having the VM and the code editor visible simultaneously means a command can be run, the output watched in real time, and the result documented — without context-switching. Screenshots are captured the moment a step completes, while the output is still on screen. This mirrors how a working sysadmin operates: one screen for the management plane, one for the control plane.

### Notepad — Credential & Connection Tracking

Windows Notepad is kept open throughout each lab as a lightweight scratchpad for ephemeral session details:

- **VM public IP** — copied from the Azure portal the moment deployment completes, ready for RDP and README documentation
- **Administrator password** — on hand for the duration of the session without returning to the portal
- **Resource names** — VM name, resource group, VNet, NSG — noted as they are created for consistent reference across scripts, commands, and docs

This is an intentional separation: Notepad holds throwaway session values; VS Code and Git hold everything that should persist. Same pattern as a scratch notepad during a production incident — useful in the moment, cleared when the work is done.

---

## AI-Assisted Workflow

Two AI tools are used in tandem, each serving a distinct role.

### Gemini AI — Live Tab Context

Gemini AI can **read directly from open browser tabs**, which makes it exceptionally useful during lab execution. With the Azure portal and lab document open in Chrome, Gemini can reference either in real time without requiring copy-paste. In practice this means:

- Lab instructions can be clarified without breaking focus from the terminal
- Azure portal state — resource status, deployment logs, error messages — can be fed to Gemini for immediate troubleshooting
- Multiple open tabs (resource group view, NSG rules, VM overview, deployment log) can be cross-referenced without switching windows

This is particularly valuable during deployment steps where portal state changes quickly and questions about it need fast answers.

### Claude (Cowork) — Documentation & Structured Output

Claude is used for producing all written deliverables: READMEs, SOPs, architecture diagrams, and documents like this one. Claude works from the lab document content, screenshots, and project instructions to generate consistently formatted, portfolio-quality output. The combination of the two tools means lab execution and documentation happen in parallel rather than sequentially.

---

## Troubleshooting Approach

When something doesn't work as expected, the process follows a structured sequence:

1. **Read the error before doing anything.** PowerShell and Azure CLI errors usually state the exact reason for failure. A `ParameterNotFound` and an `AuthorizationFailed` need completely different responses — understanding *what* failed comes before acting on it.

2. **Check context first.** Is the session running as Administrator? Is the required module loaded? Is the VM in the right power state? Many lab errors are prerequisite problems, not command problems.

3. **Use Gemini for live portal errors.** If the Azure portal is showing a failed deployment or a VM alert, Gemini reads the tab and explains it without leaving the portal — faster than copying error codes into a search bar.

4. **Isolate before re-running.** If a multi-step script fails partway through, the whole script doesn't get re-run. The last successful step is identified, the current environment state is assessed, and execution continues from that point — avoiding duplicate resources or conflicting configurations.

5. **Document errors and fixes inline.** Non-obvious workarounds or expected errors get noted in the SOP at the relevant step. This makes the runbook more realistic and more useful than one that assumes perfect conditions. Examples: the DNS delegation warning during AD DS forest promotion is expected in isolated lab environments; the `Computers` OU already existing by default produces a non-breaking error that can be ignored.

---

## Learning Methodology — SOP Recreation

The most deliberate part of this workflow is the **SOP recreation process**. Rather than executing a lab and moving on, the lab instructions are rewritten from scratch as a personal SOP — in my own words, with my own structure, and with explanations added at every step.

### Why Recreate Instead of Reuse?

The original lab document tells you *what* to run. The recreated SOP forces you to explain *what it does, why it's done that way, and what would happen if it were done differently.* Being able to execute a command and being able to explain it to someone else are different skills. Writing the SOP closes that gap.

### The What / Why / How Framework

Every command in the SOP is annotated with three layers:

| Layer | Purpose |
|---|---|
| **What** | The command and its syntax — clean and copy-ready |
| **Why** | The business or technical reason this step exists |
| **How** | The mechanism — what is actually happening on the system, where data goes, what must be true before the next step can succeed |

### Example — Lab 1 Forest Promotion

```powershell
Install-ADDSForest `
    -DomainName 'lab.local' `
    -DomainNetBiosName 'LAB' `
    -InstallDns:$true `
    -SafeModeAdministratorPassword $DSRMPassword `
    -Force:$true
```

**What:** Promotes the server to a domain controller and creates a new Active Directory forest.

**Why:** A forest is the security and replication boundary for the entire AD environment. Without it there is no domain, no DNS namespace for AD to register services in, and no database to store identities.

**How:** The cmdlet installs the AD DS database (NTDS.dit), configures SYSVOL replication, promotes DNS, and triggers a mandatory reboot to finalize the role activation. The DSRM password is required in case the domain controller needs to be recovered in Directory Services Restore Mode.

---

This level of annotation transforms a script into a reference document — something that can be returned to months later and still be understood without re-executing anything.

### The Compounding Benefit

Each lab builds on the last. By Lab 3 or 4, when a new lab uses a concept introduced in Lab 1 — like Distinguished Name path syntax or Group Policy linking — it isn't re-learned, it's extended. The SOP library becomes a personal knowledge base that grows alongside the lab series.

---

## Repo & File Conventions

Every lab follows the same folder structure:

```
lab-name/
├── README.md                  # Business-context walkthrough with screenshots
├── lab-name-sop.txt           # Annotated SOP with what/why/how per command
├── scripts/
│   └── deploy-script.ps1      # Automation script(s)
└── screenshots/               # PNGs captured during execution
    └── architecture.svg       # SVG architecture diagram (consistent style across all labs)
```

All labs are tracked in Git from the first commit and pushed to GitHub under [`sammathaadams`](https://github.com/sammathaadams), making the progression of the series publicly visible as a portfolio.

---

## Labs in This Series

| Lab | Topic | Repo |
|---|---|---|
| Lab 1 | Automated Active Directory DS Deployment on Azure | [active-directory-lab1](https://github.com/sammathaadams/active-directory-lab1) |
| Lab 2 | Network Traffic Analysis with Wireshark | [wireshark-lab2](https://github.com/sammathaadams/wireshark-lab2) |
| Lab 3 | *(in progress)* | — |
| Lab 4 | *(in progress)* | — |
| Lab 5 | *(in progress)* | — |

---

## Related Docs in This Repo

- [`git-basics.md`](git-basics.md) — Core Git commands used across the lab series with explanations
