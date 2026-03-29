# Lightweight Evaluation Loop

**DRNT Specification Addendum**

*Personal-Scale Quality Feedback for the Local Orchestrator*

**v2 — March 2026 | DRNT V1 Scope**

# 1\. Design Rationale

The Federal-Regulatory Orchestrator uses a formal evaluation harness with golden task suites of 50–200 curated test cases per workflow category, SSDF-governed template management, and structured override analytics reported to supervisors. That level of instrumentation is justified by the regulatory environment: inspectors run variations of the same task types thousands of times per day, errors propagate into safety-critical enforcement actions, and regulatory interpretation must reflect the organization’s position rather than prevailing opinion.

DRNT operates under a fundamentally different consequence and repetition profile. Most errors are recoverable because the human is the immediate consumer of every result. Question diversity is high and repetition is low—the same question rarely appears twice in the same form. Users are not IT specialists and will not tolerate evaluation workflows that feel like work. These constraints rule out formal task suites and structured evaluation forms, but they do not eliminate the need for quality feedback. Without a feedback loop, the system cannot improve its routing decisions, context selection, or prompt packaging over time.

The Lightweight Evaluation Loop provides quality feedback through three levels of decreasing user effort and increasing system inference. The design constraint is that feedback must be *cheaper than the workaround*. When a chat-based AI gives a bad answer, the user’s current workaround is to rephrase and resubmit—30–60 seconds of typing. If the feedback mechanism takes less effort than rephrasing, people will use it because it is the path of least resistance, not because they are being diligent about system improvement.

# 2\. Three-Level Feedback Model

## 2.1 Level 1: Heuristic Telemetry and Anomaly Detection (Zero User Effort)

Because every interaction is a discrete job with a submit-process-deliver cycle, the system already knows the structure of what happened. It does not need the user to describe the outcome—it can detect anomalies and capture telemetry from behavior. These signals are heuristic indicators, not quality inferences. Level 1 data identifies patterns worth investigating; it does not diagnose causes.

**Signals captured:**

* Result acted upon. The user forwards, saves, copies, or otherwise uses the delivered result. This is a positive quality signal.
* Result ignored. The user reads the result and takes no action. Weak negative signal—may indicate low relevance or bad timing rather than bad quality.
* Resubmission. The user submits the same or a closely related request shortly after receiving a result. This is the strongest negative signal: the first answer missed badly enough that the user chose to try again. The system logs the original job, the resubmission, and the delta between them.
* Time-to-action. The elapsed time between result delivery and user action. Fast action on a result correlates with quality; long delays may indicate the user needed to verify or rework the output.
* Follow-up pattern. A job that triggers a chain of related follow-up jobs may indicate the original result was incomplete or the packaging missed relevant context.

Level 1 runs silently from day one. It requires no UI, no user training, and no opt-in. It is a byproduct of the job-based UX and the audit log that already exist in v1 scope.

**Architectural constraint:** No V2 automated adaptation—routing weight changes, retrieval threshold tuning, preference profile updates—may be triggered by Level 1 signals alone. Level 1 identifies anomalies. Level 2 and Level 3 diagnose causes. Automated adaptation requires corroboration from Level 2 or Level 3 before the system acts on any Level 1 pattern.

This constraint exists because the evaluation loop performs inference from behavior to cause, and that inference is subject to the same ambiguity risks the architecture governs elsewhere. When the system observes that a result was ignored, it has detected an event, not diagnosed a failure. “Result ignored” could mean the answer was wrong, or it could mean the answer was a simple informational lookup that required no further action, or the user was interrupted, or the timing was bad. Inferring cause from a single behavioral signal is the same class of stealth reasoning the v5.0 reframe corrected in the local model’s core operations. The evaluation loop does not get an exemption from that discipline. Default conservative: observe and log at Level 1, diagnose only with human input at Level 2 and 3.

## 2.2 Level 2: Failure Category Taps (One Tap, One Second)

When a result is not good enough, the system does not ask “rate this response.” It presents six tap targets that correspond to the components the system actually controls. Each tap maps directly to a pipeline stage that can be adjusted.

|**Tap Target**|\*\* What It Tells the System\*\*|\*\* Component Affected\*\*|
|-|-|-|
|Wrong answer|The cloud model returned inaccurate or unhelpful content. The model choice or question framing was off.|Model routing logic, prompt template framing|
|Missing context|The result lacked information the user expected the system to include. The Context Packager failed to retrieve relevant material from local memory.|Context Packager retrieval, memory indexing|
|Too much / too little|The result was the wrong level of detail—either overwhelming or insufficient for the user’s need.|Prompt template detail parameters, user preference profile|
|Wrong format|The content was acceptable but the presentation was wrong—the user wanted a summary and got a list, or wanted structured output and got prose.|Prompt template output format specification|
|Wrong question|The system answered a different question than the one asked. Routing or intent classification misread the request scope.|Router intent classification, task type mapping|
|Used wrong context|The result included context the user didn’t want used, or relied on stale or outdated information. Covers both over-inclusion and privacy-relevant packaging failures.|Context Packager selection rules, privacy gate, memory freshness scoring|

**Device-specific presentation:** On the Apple Watch, failure taps appear as two rows of three on the job result notification—one swipe, one tap. On the iPhone, they appear as two rows of labeled buttons below the delivered result. Neither device presents a text field or form at this level.

Level 2 is optional but encouraged. The system never blocks on feedback—if the user dismisses the result without tapping anything, Level 1 passive signals still capture the behavioral outcome.

## 2.3 Level 3: Optional Voice Clarification (Phone Only, 5–10 Seconds)

Level 3 is surfaced only when two conditions are met: the user has already tapped a failure category at Level 2, and the user is on the iPhone (never the Watch). After tapping a failure category, a single prompt appears: “What was missing?” or “What was wrong?” with a voice input button.

The user says something like “It didn’t include the quote I got from the contractor last week” and the system now has raw evidence linked to the job record. In V1, this voice input is stored as-is—attached to the job in the audit log but not parsed into structured fields or resolved into specific memory objects. Resolving “the quote from the contractor last week” into a specific document reference is non-trivial and belongs in V2 once enough examples exist to build a reliable extraction layer. Similarly, “I needed this as bullet points, not a paragraph” is stored as a verbatim preference signal, not automatically applied to the preference profile until V2.

Level 3 is always optional. If the user taps a failure category and moves on without providing voice clarification, the Level 2 signal is still logged and useful. Level 3 adds specificity but is never required.

# 3\. What the Feedback Feeds

The three feedback levels produce structured data that maps to specific system decisions. Over time, the system accumulates enough signal to adjust its behavior without requiring explicit configuration.

## 3.1 Routing Optimization

The audit log records which cloud model handled each job and the quality outcome (Level 1 behavioral signal plus any Level 2/3 feedback). Over hundreds of jobs, patterns emerge: financial questions routed to Model A get resubmitted 40% of the time, but the same category routed to Model B gets used on first delivery. The system adjusts routing weights per task category based on measured first-pass acceptance rates. This is analogous in purpose to the DOT Model Performance Ledger—a lower-cost, lower-certainty quality signal built from organic usage patterns rather than formal benchmarking. The diagnostic precision is lower, but the signal is appropriate to an environment where most errors are recoverable and the human reviews every result.

## 3.2 Context Selection Improvement

“Missing context” taps (Level 2) and voice clarifications (Level 3) tell the Context Packager where its retrieval failed. If users repeatedly flag missing context on jobs where the relevant material exists in local memory, the system can identify retrieval gaps—poor indexing, wrong relevance thresholds, or context class tags that don’t match how the user thinks about their own data. These signals feed directly into memory indexing adjustments and retrieval threshold tuning.

## 3.3 Preference Learning

“Too much / too little” and “Wrong format” taps accumulate into a user preference profile that the Context Packager applies to prompt templates. If a user consistently flags results as too verbose, the system adjusts prompt framing to request concise output. If they consistently want structured output (lists, tables) rather than prose, the default format shifts. These preferences are stored locally and applied per job without the user needing to specify them each time.

## 3.4 Transparency Moments

When the system adjusts behavior based on accumulated feedback, it tells the user. Not as a dashboard or report, but as brief contextual notes attached to job results:

* *"**I routed this to Claude instead of ChatGPT because your last three similar questions got better results there.**"*
* *"**I included your contractor quote this time because you flagged missing context last week.**"*
* *"**I kept this concise based on your preference for shorter responses on this type of question.**"*

These moments close the feedback loop visibly. The user sees that their input changed system behavior, which reinforces the habit of providing feedback. This is WAL promotion logic expressed as a useful sentence instead of a governance metric—the personal-scale equivalent of the DOT supervisor dashboard.

## 3.5 Doctrinal Constraint: Behavioral Acceptance Is Not Correctness

The feedback loop measures behavioral acceptance: whether the user acted on a result quickly, ignored it, or resubmitted. Behavioral acceptance is a utility and convenience metric. It is not a correctness metric. A result can be accepted quickly because it is accurate. It can also be accepted quickly because it is confidently wrong, pleasantly written, or tells the user what they wanted to hear.

This creates a perverse incentive if left ungoverned. The system could optimize for fast acceptance by producing shorter answers (fewer “too much” complaints), more assertive tone (fewer follow-ups), and satisfying prose over rigorous analysis. These optimizations improve behavioral metrics while degrading answer quality.

**Architectural rule:** Preference learning from the feedback loop cannot override task-calibrated quality constraints. If a task type requires detailed analysis, the system does not truncate output because the user’s historical preference skews toward brevity. If a financial question requires hedging and uncertainty language, the system does not produce assertive answers because assertive answers get fewer resubmissions. Preference learning adjusts formatting, detail level, and presentation within the bounds of what the task requires. It does not adjust the substance of what a correct answer looks like.

# 4\. Relationship to WAL Governance

The feedback loop provides the quality signal that WAL promotion decisions depend on. At DOT scale, WAL promotion requires formal thresholds: minimum transaction count, minimum approval rate, zero sensitive data mishandling. At personal scale, the same logic applies but the measurement source is the Lightweight Evaluation Loop rather than a formal evaluation harness.

A capability operating at WAL-0 (recommend only) accumulates Level 1 behavioral signals on every job. If first-pass acceptance is consistently high across a task category—results are acted upon, resubmission rate is low—that’s evidence supporting promotion to WAL-1 (draft artifacts). If the system begins drafting at WAL-1 and “wrong answer” taps spike, that’s a demotion trigger back to WAL-0.

The feedback loop makes WAL governance empirical at personal scale without requiring the user to manage trust levels directly. The system proposes promotions based on measured performance; the user approves or declines. Demotions are automatic when feedback signals degrade. The user never needs to understand WAL mechanics—they just see the system getting more or less autonomous based on how well it performs.

# 5\. V1 and V2 Scope Boundary

|**V1 (Ship It)**|\*\* V2 (Earned Through Usage)\*\*|
|-|-|
|Level 1 heuristic telemetry: resubmission detection, time-to-action, result usage tracking (observation and anomaly detection only, not quality inference)|Automated routing weight adjustment based on accumulated model performance data (requires Level 2/3 corroboration of Level 1 patterns)|
|Level 2 failure category taps on iPhone and Watch|Retrieval threshold tuning from missing-context signals|
|Level 3 optional voice clarification on iPhone (raw evidence capture, stored verbatim without structured extraction)|User preference profile applied to prompt templates automatically|
|All feedback stored in append-only audit log with job linkage|Transparency moments surfaced on job results|
|Basic reporting: the user can see their own feedback history if they want it|WAL promotion proposals based on feedback-derived quality metrics|

V1 captures all three levels of feedback and logs them. V1 is explicitly framed as observation and capture, not inference. V2 acts on accumulated signals automatically, but only where Level 1 heuristic telemetry is corroborated by Level 2 or Level 3 diagnostic input. This sequencing is deliberate: the system needs a baseline of real, human-diagnosed feedback data before it can make reliable adjustments. Premature automation of routing or preference changes based on sparse or uncorroborated behavioral data produces erratic behavior that erodes trust faster than manual operation builds it.

# 6\. What This Is Not

It is not a rating system. There are no stars, scores, or thumbs. The feedback categories map to system components, not user satisfaction abstractions.

It is not a training pipeline. The local model is not fine-tuned from feedback data. Feedback adjusts routing, retrieval, and packaging configuration—it does not modify model weights.

It is not a golden task suite. There are no curated test cases. Quality measurement comes from organic usage patterns, not controlled benchmarks. This is appropriate for personal scale where question diversity is high and repetition is low.

It is not optional in concept, only in user participation. Level 1 passive signals run whether the user engages with Level 2 and 3 or not. The system always has some quality data. Active feedback makes it better, but silence is still informative.

*DRNT Specification Addendum | Lightweight Evaluation Loop | March 2026*

