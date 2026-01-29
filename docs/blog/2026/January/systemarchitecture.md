---
type: blog
description: The EverestOS architecture laid out as its currently planned
date: 2026-01-26
---

# Everest OS Planned Architecture

!!! note
    This is only the architecture as I have it currently planned, this is subject to change as I plan it further, gather suggestions and make it in the future


``` mermaid
graph LR
  A[EFI/Hard Startup] --> B[EFI/Soft Handover] --> C{Is code detected in first drive location?};
  C --> |Yes| D[Show missing drive logo];
  E@{ shape: lin-cyl, label: "Disk Storage"} --> C
```
