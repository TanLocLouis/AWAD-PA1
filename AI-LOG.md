# AI-LOG.md — Team TicketBox

> Course: CSC13114
> Milestone: PA#1 — Proposal and Planning  
> Team: 23120050 (Nguyễn Nhật Khang), 23120057 (Lê Tấn Lộc), 23120066 (Võ Thiện Nhân)

---

## Data Privacy & LLM Compliance (Rule 6)

- **Data leaving the system:** Only the raw text extracted from publicly distributable artist press-kit/rider PDF files (bios, song lists, public tour requirements) is sent to Google AI Studio (Gemini 1.5 Flash API).
- **Why acceptable:** These PDF documents are marketing and press materials created specifically by artist agencies for public release and event promotion. No personally identifiable information (PII), customer credentials, payment tokens, or internal database records ever leave the server environment.

---

## 2026-10-05 — Problem definition & Architecture pivoting
**Tool: ChatGPT.**  
Asked for: Critique our initial microservices proposal and reframe it into a modular monolith focused on flash-sale ticket concurrency.  
Kept: The problem framing regarding database connec05on exhaustion during flash sales.  
Changed: Condensed the user persona to a specific event promoter (Minh) and ticket buyer (Linh) with a single-sentence problem statement.  
Rejected: Suggested Kafka event streaming and distributed saga patterns; these add operational overhead contrary to our modular monolith scope.  
By hand: The exact database transaction and Redis reservation boundary definitions.

**Tool: Claude 3.5 Sonnet.**  
Asked for: Review our draft proposal against the PA#1 rubric and suggest honest self-assessment deductions.  
Kept: The suggestion to deduct points on risks and tech choices for missing an offline fallback model and multi-server Redis failover strategy.  
Changed: Calibrated our final self-score to 98/100, ensuring every claimed mark maps directly to an identifiable section in `PROPOSAL.md`.  
Rejected: Its initial suggestion to claim 100/100 across all criteria.  
By hand: The "What I Did Not Manage" reflection section detailing known limitations.

**Tool: none.**  
Written by hand: Decided to cut the native mobile app and on-site camera QR scanner entirely. Kept the project strictly web-responsive (React SPA + Express) so our three-person team can focus our semester on Redis concurrency scripts and the PDF extraction pipeline.