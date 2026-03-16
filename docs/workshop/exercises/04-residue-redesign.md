# Exercise 4 — Residue Sketching

**~20 minutes**

---

## Goal

Sketch one or two architectural residues — capabilities that would make NovaMesh more resilient to the high-impact stressors and high-drift attractors you identified.

A residue is not a point fix for a single stressor. It's an architectural investment that improves the system's ability to *adapt* when stress arrives — even stress that wasn't explicitly considered when the residue was designed.

---

## What Is a Residue?

Compare these two ways of responding to the same problem:

**Solution thinking** (not a residue):
> "Add retry logic around the AWS Rekognition API call."

**Residue thinking**:
> "A vendor-agnostic recognition interface: all internal consumers talk to an abstraction that specifies *what* is needed (face match result, confidence score), without binding to *how* Rekognition delivers it. The implementation can route to Rekognition, the internal MobileFaceNet model, or the on-device edge model based on availability and regulatory requirements."

The second is a residue because it addresses multiple stressors (API change, outage, regulatory prohibition, vendor pricing) and doesn't require knowing *which* of those stressors will actually arrive.

---

## What to Do

From your matrix and attractor discussion, pick 1–2 residue candidates — the architectural changes that feel like they'd address the broadest set of your identified stressors.

For each, sketch out:

**What is this capability?**
Describe what it *is*, not how to implement it. 2–4 sentences.

**Which stressors does it address, and how?**
For each stressor in your set that this residue helps with: what would happen differently when the stressor arrives?

**Which attractor does it resist?**
Does this residue make it harder for the system to drift into A1, A2, or A3?

**What survives?**
Complete this sentence: *"If [stressor] arrives, the system [continues / degrades gracefully / adapts by]... because the [residue] allows..."*

**What does it cost or complicate?**
Every residue has trade-offs. Naming them honestly is part of the design.

You don't need a formal template. A paragraph and a rough diagram is enough.

---

## Criticality Test

Once you've sketched a residue, test it: introduce a stressor the residue *wasn't designed for*. Does it still help?

A few stressors to try:
- "What if a court ruling required all enrolled face embeddings to be stored only on the device, with no cloud copy?"
- "What if a major mobile OS update broke the app's push notification handling — meaning visitor alerts stop reaching homeowners?"
- "What if a security researcher published a tool that could extract face identities from recognition audit logs?"

If the residue handles these without modification, you're moving toward criticality.

---

## Closing Discussion

After sketching the residues, step back:

1. Is there anything here that's applicable to the systems you actually work on? What would the stressors and components look like for your own architecture?

2. Was there a moment in the session where an assumption about the architecture was revealed that surprised you?

3. Would you run this kind of session on your own codebase? What would you need to prepare?
