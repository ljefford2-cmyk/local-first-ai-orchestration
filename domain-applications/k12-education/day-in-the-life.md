# A Day in the Life

## Three Roles, One School Day

*How the local-first AI orchestrator serves a rural school district — a scenario walkthrough.*

> **Note:** All characters and scenarios are fictional. Every interaction is grounded in the reference architecture.

---

# Introduction

This document follows three people through a single school day in a rural Georgia district that has deployed the Local-First AI Orchestrator described in the Sovereign AI Consolidated Report. The district is fictional, but every interaction is grounded in the architecture, tier classifications, privacy safeguards, and Georgia legal requirements documented in that report.

The characters represent three levels of the district’s organizational chart — a classroom teacher, her building principal, and the district superintendent. Each scenario shows how the the district central node processes their requests differently based on complexity, data sensitivity, and legal constraints. Color-coded tier badges indicate which processing layer handled each task.

| **TIER 1** Local Deterministic | **TIER 2** Local Verification | **TIER 3** Cloud Escalation | **BLOCKED** Human Required |
| --- | --- | --- | --- |

Notice what never happens in these scenarios: no student name, no GTID, no IEP content, and no behavioral record ever reaches a cloud server. The intelligence leaves the district seat. The identity stays.

# Rachel — 5th Grade ELA Teacher

*the elementary school  |  12 years in the classroom  |  RTI Tier Lead for her grade band*

Rachel has been teaching in this district since before the school had reliable Wi-Fi. She doesn’t think of herself as a technology person. She thinks of herself as someone who needs to get 26 kids through the Georgia Milestones in May, and who lost her planning period three days this week to parent conferences. The AI system her district rolled out last fall isn’t something she had strong feelings about, one way or the other. What she noticed is that it’s fast, it doesn’t require a login she has to remember, and it doesn’t lecture her about pedagogy.

**7:15 AM — Before the Buses**

Rachel is at her desk with coffee, trying to figure out her reading groups for the week. Three students were moved into her RTI Tier 2 intervention block on Friday and she hasn’t had time to reorganize. She opens the district AI portal on her laptop and types:

*“Pull my current 2nd period ELA roster and flag any students with active RTI plans.”*

| **TIER 1 — LOCAL DETERMINISTIC** |
| --- |

The response appears in under two seconds. The the district seat node queried the ClassLink roster database, cross-referenced the RTI flags in the student information system, and returned a formatted list. No AI model was involved — this was a straight database lookup. Three names are highlighted with an RTI-2 tag beside them.

| **Privacy Note: ***Zero data left the district network. The OneRoster gate confirmed Rachel’s active section assignment before returning any student information. She cannot see rosters for classes she does not teach.* |
| --- |

**8:40 AM — First Period, Quiet Work Time**

Rachel’s students are doing independent reading. She has twenty minutes and she wants to build a vocabulary activity for tomorrow’s lesson on informational text structures. She types:

*“Create a vocabulary matching activity for 5th grade ELA using ELAGSE5RI4. Include 10 academic vocabulary words from informational text. Format it as a printable worksheet.”*

| **TIER 3 — CLOUD ESCALATION** |
| --- |

This request requires creativity — generating original instructional content aligned to a specific Georgia standard. The the district seat Orchestrator first pulled the exact text of standard ELAGSE5RI4 from the local GSE CASE database (the vector copy of the Georgia Standards of Excellence ingested from GaDOE’s OpenSALT API). It then constructed a Context Package: the standard text, the grade level, the activity format, and the word count — nothing about Rachel, her school, or her students. That package was sent to the cloud model, which returned a polished vocabulary worksheet. The Orchestrator formatted it and presented it in the portal.

Rachel downloads the PDF, changes two of the words to ones her class has already encountered, and sends it to the copier queue. Total time: four minutes.

| **Privacy Note: ***The cloud model received: a GSE standard code, a grade level, and an activity type. It never received Rachel’s name, school name, student names, or any classroom-specific data. The “keys” to this content stayed on the the district seat server.* |
| --- |

**10:30 AM — After the Spelling Test**

Rachel has a stack of 26 short-answer responses from yesterday’s reading comprehension assignment. She doesn’t need the AI to grade them — she knows her kids’ writing. What she needs is help organizing the data. She photographs the papers with her phone (the district’s scanning app converts them to text), uploads the batch, and types:

*“Extract the scores from these responses and organize them into a table: student first name, question number, and score out of 4.”*

| **TIER 2 — LOCAL VERIFICATION** |
| --- |

This is structured data extraction — the local Llama model running on the the district seat server can handle it without cloud assistance. The system reads the scanned text, identifies the scoring pattern, and returns a clean table. Nothing left the building. Rachel copies the table into her gradebook spreadsheet.

| **Privacy Note: ***Because only first names were used and the task was handled entirely by the local model on the the district seat server, no data of any kind reached the internet. This is the “electricity only” cost tier.* |
| --- |

**1:45 PM — Planning Period (Finally)**

Rachel receives an email from the Special Education coordinator asking her to review a student’s Present Levels of Academic Achievement and Functional Performance (PLAAFP) draft and “let me know if the reading data looks right.” Rachel pulls up the draft in the AI portal and types:

*“Compare the reading scores listed in this PLAAFP draft against the assessment data I have access to for this student. Flag anything that doesn’t match.”*

| **TIER 2 — LOCAL VERIFICATION** |
| --- |

The the district seat node ran a local verification: it cross-referenced the scores in the PLAAFP text against the student’s actual assessment records in the SIS. It found one discrepancy — a Lexile score from October that had been updated in November but the draft still reflected the old number. It flagged the line. Rachel emails the coordinator with the correction.

At no point did Rachel ask the system to write an IEP goal or make a placement recommendation. If she had, here’s what would have happened:

| **BLOCKED — HUMAN REQUIRED** |
| --- |

The system would have returned: *“This task involves high-stakes decision making and requires human judgment per District Policy and IDEA. Please consult the Special Education Department.”*

| **Privacy Note: ***The PLAAFP data never left the district seat. The local model compared two on-premise data sources. The system has no network path to GO-IEP write endpoints — this is enforced at the firewall level, not by a software toggle.* |
| --- |

**3:15 PM — After Dismissal**

Rachel needs to write a parent email about an upcoming field trip to the Okefenokee Swamp Park. She types:

*“Draft a parent notification email about a field trip to a local nature center on March 14. Include permission slip reminder, lunch instructions, and bus departure time of 8:30 AM. Friendly tone, one page.”*

| **TIER 3 — CLOUD ESCALATION** |
| --- |

The Orchestrator classified this as a creative writing task requiring natural language generation and routed it to the cloud. The Context Package contained only the trip details Rachel typed — no student names, no school name, no class roster. The cloud model returned a warm, professional email draft. Rachel edited the opening line to sound more like herself, added the actual field trip location, and sent it through the school’s parent communication system.

Rachel’s total AI interactions today: five. Cost to the district in cloud API fees: approximately six cents. Time saved: she estimates about ninety minutes.

# James — Principal, the elementary school

*Building administrator  |  Former middle school math teacher  |  8th year as principal*

James runs a building with 410 students, 38 certified staff, and a front office that handles everything from bus complaints to custody disputes. He is not an instructional technology enthusiast. He is a person who has 23 minutes between his 8:00 AM arrival and the first parent phone call, and he needs those minutes to count. What he cares about is whether a tool reduces the number of problems that land on his desk or adds to them.

**7:50 AM — Before the Day Begins**

James opens the AI portal and asks what he asks every morning:

*“Show me today’s attendance flags, any bus delays, and the master schedule changes for this week.”*

| **TIER 1 — LOCAL DETERMINISTIC** |
| --- |

Three database lookups, all resolved locally in under a second. He sees that Bus 14 is running twelve minutes late (pulled from the transportation system), that one teacher called in sick and the sub has already been confirmed (pulled from the absence management system), and that Thursday’s assembly has been moved to the gym due to a facilities conflict. None of this required AI reasoning. The the district seat node simply queried the right databases and formatted the results.

| **Privacy Note: ***All data sources are internal district systems accessed over the fiber WAN. No cloud call. No cost.* |
| --- |

**9:30 AM — The FTE Problem**

It’s FTE count week. The district data coordinator sent James a spreadsheet of his building’s enrollment records for pre-validation before submission to GaDOE. Last October, the district found three coding errors that delayed their FTE funding by six weeks. James is not going to let that happen again. He uploads the spreadsheet and types:

*“Run the FTE pre-validation checks on this file. Flag any records that would trigger a GaDOE edit rejection.”*

| **TIER 2 — LOCAL VERIFICATION** |
| --- |

The local model on the the district seat server ran the spreadsheet against the GaDOE’s published edit check rules — more than 200 validation conditions covering enrollment dates, program codes, grade-level assignments, and funding category flags. It returned four flagged records: two students with mismatched grade-level codes, one with a missing withdrawal date from a transfer, and one where the special education funding code didn’t align with the services documented in the system.

James forwarded the flags to his registrar and the SpEd coordinator with a note: “Please verify and correct before Thursday.” What used to take his data clerk an entire afternoon of manual cross-referencing took the system eleven seconds.

| **Privacy Note: ***FTE data contains student names and GTIDs. Because this was a Tier 2 local task, that data never left the the district seat server. The validation rules themselves are public GaDOE documentation stored in the local vector database.* |
| --- |

**11:00 AM — A Difficult Conversation**

James has a meeting at 11:30 with a parent who is upset about a classroom incident. He wants to prepare talking points, but the situation involves a student’s behavioral record. He types:

*“Summarize the behavioral incidents for Student ID 4472 this semester and suggest talking points for a parent conference.”*

The system splits this into two operations.

| **TIER 1 — LOCAL DETERMINISTIC** |
| --- |

First, the the district seat node pulls the behavioral incident log from the SIS — a local database query. James sees the timeline: dates, descriptions, and prior actions taken. This is Tier 1 data retrieval.

| **TIER 3 — CLOUD ESCALATION** |
| --- |

Second, for the talking points, the Orchestrator recognized that this requires nuanced communication advice. But the Context Packager intervened heavily. What reached the cloud was:

*“A school administrator is meeting with a parent about a student who has had three behavioral incidents this semester involving classroom disruption. The most recent involved a verbal altercation with a peer. Suggest professional, empathetic talking points for the parent conference.”*

No name. No student ID. No school. No specific incident details beyond the general pattern. The cloud model returned thoughtful, de-escalation-oriented talking points that James reviewed, adjusted for the specific family dynamic he knows from experience, and printed for his reference.

| **Privacy Note: ***The behavioral record stayed on the the district seat server (Tier 1). The cloud received only an anonymized, generalized scenario description. The Context Packager’s keyword filter flagged “behavioral incidents” and “student ID” in the original prompt and stripped them before the cloud call.* |
| --- |

**2:00 PM — Budget Season**

James is working on next year’s Title I budget request. He’s drafted a line item for supplemental reading materials and needs to verify the funding code. He types:

*“Is object code 611-1100 allowable for supplemental instructional materials under Title I, Part A?”*

| **TIER 2 — LOCAL VERIFICATION** |
| --- |

The local model checked the object code against the district’s Title I allowable expense list and the GaDOE funding manual stored in the vector database. It confirmed the code is valid for instructional materials and noted that it requires documentation linking the expense to the school’s Comprehensive Needs Assessment. James added that note to his budget narrative.

| **Privacy Note: ***No student data involved. Budget codes and funding rules are public information processed entirely on-premise.* |
| --- |

**4:30 PM — End of Day**

James needs to send a weekly update to the superintendent. Rather than spending forty-five minutes writing it from scratch, he types:

*“Draft a weekly principal’s update email. Highlights: FTE pre-validation complete with 4 flags being resolved, successful fire drill Tuesday, one sub coverage gap filled, and the spring field trip schedule is finalized. Professional, brief.”*

| **TIER 3 — CLOUD ESCALATION** |
| --- |

The Orchestrator sent the bullet points — no student names, no staff names, just the operational facts James typed — to the cloud model. It returned a clean, concise email that James reviewed, added his sign-off to, and sent.

James’s total AI interactions today: six. Cloud API cost: approximately eight cents. He estimates the FTE pre-validation alone saved his building three to four hours of staff time.

# Dr. Carolyn Webb — Superintendent

*District Office  |  Former high school principal  |  4th year as superintendent  |  Title I rural district*

Dr. Webb does not use the AI system the way Rachel and James do. She doesn’t build worksheets or look up bus routes. She is responsible for a $28 million budget, a Board of Education that meets publicly twice a month, and a community that is watching closely to see whether this “AI thing” was worth the investment. Her interactions with the system are strategic, not operational. She is asking the questions a Board member would ask — and she wants answers she can defend.

**8:00 AM — Board Prep**

The Board meets next Tuesday. Dr. Webb needs to present a mid-year update on the AI pilot’s performance. She opens the portal and types:

*“Pull the AI system usage dashboard for this semester. I need total queries by tier, cloud API costs month-over-month, number of active staff users, and any privacy incidents logged.”*

| **TIER 1 — LOCAL DETERMINISTIC** |
| --- |

The the district seat node queried its own audit logs and the Grafana monitoring layer. The response came back as a formatted summary: 14,200 total queries this semester. 61% resolved at Tier 1, 24% at Tier 2, 15% routed to Tier 3 cloud models. Total cloud API expenditure: $2,340 — running 22% under the projected budget. Active staff users: 187 of 312 certified and classified employees. Privacy incidents: zero. Blocked high-stakes attempts: 47 (all returned the appropriate policy message; none resulted in data exposure).

Dr. Webb copied the summary into her Board presentation deck. The numbers tell the story she needs: the system is being used, it’s under budget, and it’s enforcing the guardrails.

| **Privacy Note: ***Usage analytics are aggregated and contain no student or staff PII. The audit log is append-only and cannot be modified by the AI agent itself, satisfying FERPA 34 CFR § 99.32 audit trail requirements.* |
| --- |

**10:00 AM — A Call from RESA**

The RESA director calls to discuss expanding the shared AI engineering contract. Two neighboring districts want to join the consortium. Dr. Webb needs a quick financial projection. She types:

*“If we expand the RESA AI consortium from 3 districts to 5, and total cloud API usage increases proportionally, what does our per-district cost share look like over the next two fiscal years? Current annual RESA engineering cost is $90,000 split three ways.”*

| **TIER 3 — CLOUD ESCALATION** |
| --- |

This is a reasoning task — it requires the cloud model to work through financial projections with multiple variables. The Context Package contained only the numbers Dr. Webb typed. No district names, no student counts, no budget details beyond what she explicitly provided. The cloud model returned a clear cost table showing the per-district share dropping from $30,000 to $18,000 annually, with scenario modeling for API cost growth at 10%, 20%, and 30% increases.

Dr. Webb reviewed the projections, adjusted one assumption based on her knowledge of the other districts’ likely usage patterns, and emailed the RESA director with a counter-proposal.

| **Privacy Note: ***No student or operational data was involved. The cloud received only abstract financial figures typed by the superintendent. This is a clean Tier 3 use case with zero privacy risk.* |
| --- |

**1:00 PM — The Parent Data Request**

A parent has submitted a formal request under Georgia’s Student Data Privacy Act (O.C.G.A. § 20-2-667) to see what data the AI system has processed about their child. Dr. Webb needs to respond within three business days. She types:

*“Generate a Data Receipt for Student ID 2281. Show all AI system interactions involving this student’s data for the current school year, including tier classification, whether any data was sent to cloud models, and the anonymization method used.”*

| **TIER 1 — LOCAL DETERMINISTIC** |
| --- |

The the district seat node searched the append-only audit log for any interaction tagged with this student’s identifier. It returned a clean report: the student’s data appeared in 12 interactions this year, all initiated by the student’s assigned teachers. Nine were Tier 1 (roster lookups, attendance checks). Three were Tier 2 (reading assessment data verification). Zero were Tier 3 — meaning this student’s data never left the district’s network in any form, anonymized or otherwise.

Dr. Webb forwarded the Data Receipt to the parent with a cover letter. The entire response took less than fifteen minutes. Under a traditional SaaS model, generating this kind of per-student audit trail would have required a formal request to the vendor — if it were available at all.

| **Privacy Note: ***The Data Receipt was generated entirely from local audit logs on the the district seat server. The append-only log’s integrity is guaranteed by filesystem-level immutability (chattr +a). This directly satisfies the parental access requirements of O.C.G.A. § 20-2-667.* |
| --- |

**3:30 PM — CCRPI Narrative**

The district’s CCRPI (College and Career Ready Performance Index) data is due for Board review. Dr. Webb has the numbers from the assessment office. She needs help with the narrative — the story the data tells. She types:

*“Here are our district’s CCRPI component scores for the past three years. Write a Board-ready narrative analysis identifying trends, areas of improvement, and areas of concern. Do not reference any individual school or student. Professional, evidence-based tone.”*

| **TIER 3 — CLOUD ESCALATION** |
| --- |

This is the kind of high-value Tier 3 task the architecture was designed for. Dr. Webb pasted in aggregated, district-level scores — no school names, no student data, just the published performance metrics. The cloud model returned a multi-paragraph narrative analyzing year-over-year trends in content mastery, progress, and readiness indicators.

Dr. Webb spent twenty minutes editing the draft, adding context only she would know — the impact of a staffing shortage on 8th grade math scores, the new reading intervention that started mid-year — and had a polished Board narrative ready before she left for the day.

Dr. Webb’s total AI interactions today: four. Cloud API cost: approximately twelve cents. The Data Receipt alone — generated in seconds from local audit logs — would have taken hours under any other system. The CCRPI narrative draft saved her an evening of writing.

| **Privacy Note: ***The cloud received only aggregate, district-level performance data with no school or student identifiers. The narrative analysis was reviewed and edited by the superintendent before any public use.* |
| --- |

# What Happened — and What Didn’t

Across these three people and fifteen interactions, here is what the Local-First Orchestrator did:

| **Person** | **Tier 1** | **Tier 2** | **Tier 3** | **Blocked** | **Est. Cloud Cost** |
| --- | --- | --- | --- | --- | --- |
| Rachel (Teacher) | 1 | 2 | 2 | 0* | ~$0.06 |
| James (Principal) | 2 | 2 | 2 | 0 | ~$0.08 |
| Dr. Webb (Supt.) | 2 | 0 | 2 | 0 | ~$0.12 |
| **TOTAL** | **5** | **4** | **6** | **0** | **~$0.26** |

**Rachel never attempted a blocked task in this scenario, but the document illustrates what would have happened if she had.*

Fifteen interactions. Twenty-six cents in cloud costs. An estimated five to six hours of combined staff time recovered. And across all of it, not a single student name, GTID, IEP document, or behavioral record ever reached a server outside the district’s physical control.

That is what “Route, Don’t Reason” looks like in practice. The intelligence leaves the district seat. The identity never does.

*Companion document to the Sovereign AI Consolidated Architecture **&** Implementation Report*

Prepared February 2026  |  Version 1.0