# Exercise 1 — Stressor Selection

**~20 minutes**

---

## Goal

Agree on a working set of 5–7 stressors to use in the incidence matrix. The set should feel interesting and architecturally revealing — not just the most probable scenarios.

---

## What to Do

Read through the [Stressors Catalogue](../../residuality-theory/stressors-catalog.md) together. For each example stressor, briefly discuss:
- Would this stressor have a significant impact on the NovaMesh architecture?
- Is there anything surprising about *which* components it affects?

Pick the stressors that generate the most interesting discussion — not the most likely ones.

Then generate at least one stressor that isn't in the catalogue. Use these prompts if you need a starting point:

| Category | Prompt |
|---|---|
| Technical | "What dependency in the recognition pipeline is everyone assuming will always work?" |
| Regulatory | "What biometric or privacy regulation could emerge in the next 2 years that would hit hardest?" |
| Competitive | "What could Ring or Google do that NovaMesh currently has no answer to?" |
| Organisational | "What people or knowledge situation would most cripple NovaMesh's ability to operate safely?" |
| Environmental | "What physical-world event would make the fact that this product controls a door lock most dangerous?" |
| Reputational | "What could go wrong with facial recognition that would be hardest to explain to a court?" |

Finally, try to generate one **implausible stressor** — something that feels unlikely but is technically conceivable. These often reveal the most interesting architectural assumptions. Some seeds:

- "What if face recognition accuracy dropped to 40% for all customers due to a model update?"
- "What if AI liability legislation made software companies responsible for all physical harms caused by their AI?"
- "What if a court ruling required all enrolled face embeddings to be stored only on the device, with no cloud copy?"

Write the implausible stressor on a different coloured sticky note or in a different colour on the whiteboard so it's easy to identify later.

---

## Output

A list of 5–7 stressors (with short names) to use in Exercise 2. Include at least one from different categories, and include the implausible stressor.
