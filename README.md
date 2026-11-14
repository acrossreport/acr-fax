# ACR FAX

[日本語](README.ja.md)

ACR FAX combines ACR, a report-rendering engine, with HylaFAX to let external
systems (ordering workflows, work reports, and the like) trigger fax sends.
It also lets you send HylaFAX's usual targets — screen captures, Office files
(Excel/Word), and PDFs — through the same system.

## Highlights

- **Builds on HylaFAX as-is.** Rather than reimplementing the fax protocol,
  this project uses HylaFAX, which has been battle-tested for decades, as the
  server foundation. HylaFAX itself is never patched; the binaries are built
  from unmodified upstream source, to keep compatibility and make future
  upgrades easy to pull in.
- **Verified modems:** currently verified with USB-connected Class 1 / 1.0 / 2
  fax modems — specifically [Planex](https://www.planex.co.jp/product/accessories_modem.html)
  and the AGPtek External USB2.0 FAX MODEM (analog, dual-port). Verification
  with PCI/PCIe multi-port serial boards is planned.
- **A dedicated setup tool with automatic modem detection.** At install time
  and whenever a modem is swapped, a dedicated GUI tool scans connected
  modems and applies the required configuration (see "Modem setup" below).
- **The client talks to the server at the protocol level**, not by wrapping
  external commands, for lightweight, fast operation.
- **The client is built with Avalonia UI.** A single codebase covers
  Windows/macOS/Linux on both x64 and ARM64.
- **Dial settings and user management can also be changed remotely** from the
  client UI over SSH, without going to the server machine.
- **The client app is self-contained.** No separate runtime install is
  needed; just run the distributed executable.

## Overview

```mermaid
flowchart TB
    ACR["ACR engine<br/>(auto-sends reports/screens as fax)"]
    C1["Client #1<br/>(Windows / macOS / Linux)"]
    C2["Client #2<br/>(Windows / macOS / Linux)"]
    C3["Client #N<br/>(Windows / macOS / Linux)"]
    HF["HylaFAX<br/>(single Ubuntu server)"]
    PSTN[("Phone line / remote fax machine")]

    ACR --> C1
    ACR -- "auto-submits a send job" --> C2
    ACR --> C3
    C1 --> HF
    C2 -- "send jobs / shared received faxes & history" --> HF
    C3 --> HF
    HF <--> PSTN
```

- **There is one server** (running HylaFAX on Ubuntu). The fax modem/line
  connects here.
- **Multiple clients** can connect to that single server. Any client can send
  a fax, and received faxes and send history look the same from every client
  (no single client "owns" that data).
- Besides manual sending, clients also support **automatic sending driven by
  the ACR engine** (auto-generate a PDF from report data and fax it without
  manual steps).

## Requirements

- **Server: Ubuntu Desktop 24.04 LTS is recommended.** A physical machine (or
  equivalent) is the baseline, since a serial/USB port for the fax modem/line
  is required.
- **If only a Windows machine is available, it also works under Ubuntu 24.04
  on WSL2.** In that case, the Windows host should have at least 16GB of RAM.
- Clients support Windows / macOS / Linux (x64, ARM64).

## Modem setup

A dedicated Ubuntu GUI tool handles modem detection, udev rule generation, AT
command testing, and restarting the HylaFAX service, all in one place. Beyond
initial setup, it can also re-scan every connected line whenever a modem is
swapped or added later during operation.

> **Current status:** the client app v0.1.0 has been published early for
> testing ([Releases](../../releases/tag/acr-fax-client-v0.1.0)). Server-side
> executables aren't bundled yet, so the formal release will ship once server
> and client versions are matched as a set.

## License

- The software itself: Across Report's own license (separate)
- HylaFAX: Copyright (c) 1990-1996 Sam Leffler, Copyright (c) 1991-1996
  Silicon Graphics, Inc. (a permissive MIT/BSD-style license; bundled
  unmodified — see `COPYRIGHT` for the terms)
