# Patient Narrative

## Clinical AI Orchestration Framework — A Patient Safety Infrastructure Proposal

*Submitted by the author of the reference architecture, in the capacity of a patient at a major academic medical center.*

> **Note:** This narrative describes a real information architecture failure experienced by a real patient. Institutional identifiers, physician names, and patient ID numbers have been removed. The institution referenced is not named because the author does not speak for that institution. The medical details are the author's own and are included with his consent because the argument requires specificity to be credible. The structural problem described here is not unique to any one institution — it is a class of failure inherent in how electronic health records handle cross-specialty data assembly for complex patients.

---

## 1. Twenty Years as My Own Data Router

I am a 65-year-old patient with Stage 4 liver cirrhosis caused by hepatitis C that went undetected for decades, confirmed atrial flutter, and a medical history that spans hepatology, pulmonology, and cardiology. I have undergone two cardiac ablation procedures, a cardiopulmonary exercise test, and ongoing liver disease management. I am not a physician, not a health IT vendor, and not affiliated with any technology company.

For twenty years, I have navigated the healthcare system as a complex multi-system patient. And for twenty years, I have performed a role that no patient should have to perform: I have been the information bridge between my own physicians. I am the one who tells the cardiologist what the hepatologist found. I am the one who remembers which tests were run, when, and what they showed. I carry the longitudinal narrative of my own care in my head, because no system I have encountered — at any institution — has been able to assemble it for the physicians treating me.

This is not a new problem. But it has become a measurably worse one. Over the past five years, as electronic health records have expanded, as specialty departments have adopted increasingly specific documentation templates, and as the volume of clinical data per patient has grown, the gap between what is recorded and what is actually surfaced to the treating physician has widened — not narrowed. More data is captured than ever before. Less of it is synthesized at the point of care.

From the patient's perspective, the promise of the electronic health record era was that putting everything in one system would mean my doctors could see the whole picture. The reality is that the data goes in, but it does not come back out in a form that helps the next physician understand where I have been, what has been tried, and what the current clinical trajectory looks like across specialties.

## 2. Why I Chose This Institution

I drive ninety miles each way to receive my care at a major academic medical center. That is a deliberate choice, and the reason for it is directly relevant to what I am proposing in this document.

After twenty years of experiencing the fragmentation that comes with complex multi-system disease managed across multiple providers and institutions, I made the decision to consolidate my care at a single institution. I chose this institution because it represents, more than perhaps any other in American healthcare, the principle that integrated, multi-disciplinary care produces better outcomes than fragmented specialty silos. That is the founding idea. That is the brand promise. And for a patient managing the intersection of liver disease, cardiac arrhythmia, and pulmonary function, it is precisely the right promise.

I want to be clear about what I am saying and what I am not saying. Every physician and nurse I have encountered has been competent, attentive, and professional. The clinical judgment I have received has been excellent. The people are not the problem. The problem is that even at an institution built on the principle of integrated care, the information architecture that connects specialties has not kept pace with the complexity of patients like me — or with the volume of data those specialties now generate.

I did everything a patient can do. I chose the best institution I could find. I drove 180 miles round trip to put all of my care under one roof. And I can now document, precisely, a case where the system still could not surface the right data at the right time — not because it was missing, but because nothing assembled it.

## 3. The Evidence: A Single Documentable Case

The information disconnect I have experienced over twenty years is, by its nature, difficult to document after the fact. Patients do not typically retain the records needed to reconstruct what was and was not available to the treating physician at each encounter. I can document one case precisely, because I happened to have a wearable device that captured the onset of the event, because I retained copies of my own records, and because I have the technical background to understand what the clinical documentation reveals about the information architecture behind it.

The evidence presented here is drawn entirely from my medical records at the institution. Every date, measurement, and clinical notation referenced below can be verified against the institutional record.

### 3.1 November 2024 — Cardiopulmonary Exercise Test

In November 2024, I underwent a cardiopulmonary exercise test (CPET) at the institution. The study indication was dyspnea. The test was conducted under the institutional treadmill protocol.

**This test produced the following clinically significant data:**

| Measurement | Result |
|---|---|
| **Baseline ECG** | Normal sinus rhythm. A formal, 12-lead equivalent baseline electrocardiogram recorded under controlled clinical conditions. |
| **PVC Documentation** | PVCs ≥ 5 per minute documented as an additional ECG finding during exercise. |
| **Peak Heart Rate** | 144 bpm (92% of predicted). Normal heart rate response. |
| **Heart Rate Recovery** | 11 bpm at 1 minute post-exercise. Normal recovery. |
| **Peak VO2** | 2.62 L/min (90% of predicted). Average functional capacity. |
| **Breathing Reserve** | 23%. Normal pulmonary reserve. No evidence of pulmonary limitation. |
| **O2 Saturation** | Minimum 97% during exercise. Normal oxygen saturation response. |
| **Resting Heart Rate** | 62 bpm. |
| **Impression Line 4** | "No prior test for comparison." |

The final line of the impression is significant. The interpreting physician noted in November 2024 that there was **no prior test for comparison**. This means the November 2024 CPET — with its formal baseline ECG, its documented PVCs, its resting heart rate of 62 bpm, and its complete pulmonary function data — became the most recent and most comprehensive cardiac and pulmonary baseline in my institutional record.

### 3.2 March 2026 — Acute Cardiac Presentation

In early March 2026, my Apple Watch recorded an ECG showing a heart rate of 112 bpm — nearly double my documented resting rate of 62 bpm. The watch's algorithm noted no signs of atrial fibrillation but flagged the high heart rate. My heart rate had jumped from its normal baseline and remained elevated.

The following day, I presented to the institution's Emergency Department with a chief complaint of rapid heart rate, fogginess, and abdominal symptoms. My presenting vitals included a pulse of 123 bpm, blood pressure of 129/86, respiratory rate of 22, and oxygen saturation of 99%.

The ER physician correctly identified atrial flutter with variable block, consulted cardiology, and developed an appropriate treatment plan including a low-dose beta-blocker, Holter monitor, and outpatient cardiology follow-up. I want to emphasize: **the clinical decision-making was sound. This is not a question about physician judgment.**

### 3.3 What the Information System Did

The 12-lead EKG performed in the Emergency Department included an automated comparison generated by the EKG system. That comparison line reads:

> *"When compared with ECG of 18-May-2012 11:35, Atrial flutter has replaced Sinus rhythm"*

The system selected an ECG from May 2012 — a tracing recorded nearly **fourteen years earlier**, before either of my two cardiac ablation procedures in 2023 — as the comparison baseline for an acute cardiac presentation in 2026.

The November 2024 cardiopulmonary exercise test — performed at the same institution, containing a formal baseline ECG, documented PVCs, resting heart rate, and complete pulmonary function data, all directly relevant to the presenting complaint — **was not surfaced during the acute presentation.**

To be precise about the scope of the gap: the treating physician was making a clinical decision about an acute arrhythmia in a patient with Stage 4 liver cirrhosis, and the most comprehensive cardiac and pulmonary baseline in the institutional record — from 16 months earlier, in the same building — was not part of the information available to support that decision. Not because anyone failed to look for it. Because the system did not assemble it.

## 4. The Pattern Behind the Evidence

The March 2026 ER visit is the case I can document to the date, time, and comparison line. But it is not the first time I have experienced this disconnect. It is the most recent example of a pattern I have lived with for twenty years, across multiple institutions, and it illustrates something important about the nature of the problem.

The failure is not in any one department, any one physician, or any one institution. It is in the connective tissue between them. It is the absence of an information layer that can answer a deceptively simple question: *What else do we already know about this patient that is relevant to what is happening right now?*

Complex multi-system patients — those managing the interactions between liver disease, cardiac arrhythmia, pulmonary function, and metabolic complications — are precisely the patients for whom this question matters most. We are also the patients for whom it is most consistently unanswered, because our clinically relevant data is distributed across the widest number of specialty departments, documentation templates, and test types.

I chose this institution specifically to solve this problem. And if the information architecture gap exists even here — at an institution founded on the principle that integrated multi-disciplinary care is better than fragmented specialty silos — then the problem is not going to be solved by choosing a better institution. It is going to be solved by building a better information architecture.

That is what I am proposing.

## 5. What It Means to Be the Data Router

In the absence of an information architecture that assembles the cross-specialty picture, the patient becomes the data router. This is a role I have performed at every institution I have visited, and it is a role that becomes more difficult precisely when it matters most.

When I am well, I can prepare for appointments. I can bring records. I can explain to the cardiologist what the hepatologist said, remind the pulmonologist about the cardiac ablations, and flag the interactions between liver disease and cardiac medications. I can be an effective advocate for my own continuity of care.

When I arrive at the Emergency Department with a heart rate of 123 bpm and a foggy mind because my liver is not clearing ammonia efficiently, I cannot be the data router. No patient can. And that is precisely the moment when the system's ability to assemble the clinical picture on my behalf matters most.

The gap between what the institution knows and what the treating physician can see at the moment of acute care is a patient safety problem. It is a measurable one. And it is solvable.

## 6. What I Am Proposing

The accompanying technical document — *Clinical AI Orchestration Framework: Engineering Review & Integration Assessment* — provides the detailed architectural specification. This section summarizes the core idea for clinical leadership.

### 6.1 The Core Principle: Route, Don't Reason

The proposed framework does not ask AI to practice medicine. It does not generate diagnoses, recommend treatments, or replace clinical judgment.

It does one thing: it makes the data that already exists in the institutional record available to the clinician who needs it, at the time they need it, assembled in a way that supports their reasoning rather than replacing it.

The architecture is built on a principle called **route, don't reason**. The AI component functions as an intelligent routing and retrieval layer — classifying what the clinician needs, finding it in the systems of record, and presenting it with full traceability to its source. The physician reasons. The physician decides. The AI ensures those decisions are not made blind to data the institution has already collected.

### 6.2 What It Would Have Changed in My Case

When I presented to the Emergency Department with acute tachycardia and a history of atrial fibrillation, the orchestration layer would have retrieved my November 2024 cardiopulmonary exercise test as part of the relevant cardiac history. The treating physician would have had, at the point of care, a post-ablation baseline ECG for comparison, documented PVC history, a resting heart rate of 62 bpm, and pulmonary function data — all from the same institution, all within 16 months.

The physician's clinical decision — which may well have been exactly the same — would have been made with the full institutional picture available. That is the only thing I am asking for: that the physician who treats me has access to what the institution already knows.

### 6.3 What the Technical Documents Contain

The Engineering Review provides the complete architectural specification: a three-tier execution model that separates deterministic data retrieval from probabilistic synthesis, a privacy architecture that structurally protects patient data before it ever leaves the institutional boundary, a workflow autonomy framework that governs how AI-assisted capabilities earn and lose clinical trust, and a phased implementation approach that delivers measurable value at each stage without requiring the institution to accept unmanaged risk.

The accompanying Adversarial Review documents every failure mode the architecture was tested against, every concession made, and every pre-committed rollback trigger — so that institutional reviewers can evaluate not only what the system is designed to do, but where it can fail and what happens when it does.

Both documents were stress-tested by three independent AI models in adversarial review. The architecture was refined based on their findings.

## 7. What I Am Asking

**First**, read the accompanying Engineering Review and Adversarial Review. The architecture was built to meet institutional standards. It should be evaluated against them.

**Second**, consider whether the information architecture gap documented in this narrative — the gap between what the institution records and what the treating physician can see at the moment of care — represents a systemic risk that warrants a structural response.

**Third**, if the answer to the second question is yes, consider this framework as a starting point. It was built by a patient, but it was built to engineering standards, and it was designed to work within existing clinical infrastructure and governance models.

I am not asking to be involved in the implementation. I am not asking for compensation or acknowledgment. I am asking that the next patient who walks through those doors with a complex multi-system history not have to carry the narrative of their own care in their head — and that the physician who treats them not have to make decisions without the data their own institution has already collected.

I drove ninety miles to put my care in the hands of an institution I believed in. I am handing over this proposal because I believe the institution is positioned to deliver on its own promise — not just for me, but for every complex patient who makes the same choice I did.

The data already exists in the systems. It needs an architecture that delivers it.

---

*Enclosures:*

- *Clinical AI Orchestration Framework — Engineering Review & Integration Assessment*
- *Appendix R — Adversarial Review (Red Team Assessment)*
