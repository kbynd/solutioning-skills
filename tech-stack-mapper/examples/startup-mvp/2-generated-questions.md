# Generated Questions - Startup MVP Example

tech-stack-mapper reads discovery-questionnaire.md framework and generates these project-specific questions:

---

## Phase 1: Discovery Questions

Hello! You need a **metadata catalog** for your data mesh (Marketplace archetype).

To recommend the right technology, I need to understand your organizational constraints. I'll ask **15 questions** across 5 categories:

1. **Organization & Team** (5 questions)
2. **Time & Budget** (3 questions) 
3. **Operations & Support** (4 questions)
4. **Compliance & Risk** (2 questions)
5. **Political Realities** (1 question)

Let's begin:

---

### CRITICAL QUESTIONS (Must answer)

**Q1: Team Size**
How many engineers will work on the data mesh initiative?
- a) 1-10 (small team)
- b) 11-50 (mid-size)
- c) 50+ (large team)

**Q2: Deadline**
When do you need the data catalog operational in production?
- a) ASAP (within weeks)
- b) 3 months
- c) 6-12 months
- d) No hard deadline

**Q3: Who Runs Production**
Who will operate the catalog in production (run searches, handle metadata ingestion failures, manage schema changes)?
- a) No one yet / developers will handle it
- b) 1-2 people can support part-time
- c) Dedicated SRE team

**Q4: Budget**
What's your annual engineering budget?
- a) <$1M (constrained)
- b) $1M-$10M (moderate)
- c) >$10M (well-funded)

**Q5: Existing Skills**
What technologies does your team know well?
(Free-form list, e.g., "Python, AWS, Postgres, Docker, Kubernetes")

---

### IMPORTANT QUESTIONS

**Q6: Platform/SRE Team**
Do you have a dedicated platform or SRE team?
- a) No, developers handle operations
- b) 1-2 people part-time
- c) Dedicated team (5+ people)

**Q7: Learning Appetite**
Your team's appetite for learning new technologies?
- a) Low - must ship fast, stick to what we know
- b) Moderate - willing to learn if clear value
- c) High - love learning new tech

**Q8: Availability Requirements**
If the catalog goes down, what's the impact?
- a) Critical - data engineers completely blocked
- b) Business hours critical, off-hours tolerable
- c) Downtime tolerable (can work offline)

**Q9: Compliance**
Any regulatory compliance requirements?
- [ ] SOX
- [ ] HIPAA
- [ ] PCI-DSS
- [ ] GDPR
- [ ] None

**Q10: Primary Fear**
What keeps you up at night about this decision?
- a) System downtime
- b) Runaway costs
- c) Security breach
- d) Complexity (team can't operate)
- e) Vendor lock-in

---

### OPTIONAL QUESTIONS (For context)

**Q11: Funding Stage**
What's your funding/business stage?
- a) Pre-seed / seed
- b) Series A-B
- c) Series C+
- d) Profitable
- e) Enterprise

**Q12: On-Call Setup**
Current on-call setup?
- a) No formal on-call
- b) Developers rotate on-call
- c) Dedicated SRE on-call

**Q13: 2am Failure Scenario**
When the catalog breaks at 2am Saturday, what happens?
- a) Wake up a developer
- b) Wait until Monday morning
- c) Call vendor support
- d) SRE team handles it

**Q14: Technology Preference**
Your team's technology preference?
- a) Bleeding edge (love new tech)
- b) Moderate (proven but modern)
- c) Boring (stick to old reliable)

**Q15: Technology Mandates**
Any mandates from leadership?
(Free-form, e.g., "Must use AWS", "Must use open source", "None")

---

Please provide your answers, and I'll generate contextualized technology recommendations.
