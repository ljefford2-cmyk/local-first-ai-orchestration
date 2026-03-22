
[UPLOAD_INSTRUCTIONS.md](https://github.com/user-attachments/files/26163411/UPLOAD_INSTRUCTIONS.md)
# DRNT Spec Upload Instructions

## What You're Adding

Six new files, all going into `domain-applications/drnt/`:

```
domain-applications/
└── drnt/
    ├── README.md
    ├── Spec1_Audit_Event_Schema.md
    ├── Spec2_Capability_Model.md
    ├── Spec3_Context_Boundary.md
    ├── Spec4_Egress_Policy.md
    └── Spec5_Override_Semantics.md
```

## Step-by-Step (GitHub Website)

### 1. Create the first file (this creates the folder)

- Go to your repo on GitHub
- Click **Add file → Create new file**
- In the filename field, type: `domain-applications/drnt/README.md`
  (GitHub auto-creates the `drnt/` folder when you type the slash)
- Paste the contents of the README.md file
- Commit with message: `Add DRNT interface specifications`

### 2. Add each spec file

For each of the five spec files:
- Navigate into `domain-applications/drnt/`
- Click **Add file → Create new file**
- Type the filename (e.g., `Spec1_Audit_Event_Schema.md`)
- Paste the contents
- Commit each one

### 3. Update domain-applications/README.md

Edit `domain-applications/README.md` and add this entry alongside the
existing four domain sections:

---

### Personal — DRNT (Don't Reason, Navigate & Task)

Personal AI gateway — a three-device, wrist-and-pocket interface that routes
requests to the right intelligence and returns results for human decision-making.
Five interface specifications defining the audit log, capability model, context
boundary, egress policy, and override semantics.

See [`drnt/README.md`](drnt/README.md) for the full specification index.

---

### 4. Update root README.md

In the Domain Applications section of your root README, add a fifth bullet:

---

- **Personal** — five interface specifications for DRNT, a personal AI gateway implementing the full route-don't-reason architecture with append-only audit, graduated trust, and structural privacy enforcement

---

### 5. Verify

After all files are committed:
- Click into `domain-applications/drnt/README.md` and verify the links to
  each spec file resolve (no 404s)
- Click at least one spec file to confirm it renders properly
- Check the domain-applications README link to `drnt/README.md` works
