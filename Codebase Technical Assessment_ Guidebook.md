# **Codebase Technical Assessment: Guidance Document**

## **Introduction**

This guide helps you conduct effective technical debt assessments using the Codebase Technical Assessment Rubric. It provides a framework for measuring and managing technical debt. As research from books like *Accelerate* shows, actively managing these code quality dimensions is not just 'good housekeeping'—it is the most reliable predictor of a team's ability to deliver software quickly and stably, whether in a large corporation or a student-led open-source community.Whether you're a Tech Lead evaluating your own project, a mentor reviewing a team's work, or a new maintainer inheriting a codebase, this document provides practical guidance for understanding code quality and identifying improvement opportunities.

**Who This Is For:**

* Tech Leads conducting self-assessments  
* Mentors and advisors evaluating projects  
* New maintainers getting oriented to a codebase  
* Teams preparing for handoffs  
* Anyone wanting to understand technical debt systematically

**What You'll Learn:**

* How to prepare for and conduct assessments  
* Techniques for identifying technical debt  
* How to prioritize findings  
* Ways to communicate results effectively  
* Strategies for addressing debt

---

## **Understanding Technical Debt**

### **What Is Technical Debt?**

Technical debt is the implied cost of future rework caused by choosing an expedient solution now instead of a better approach that would take longer. Like financial debt, technical debt isn't inherently bad—sometimes taking on debt is the right decision to ship quickly or learn fast. The problem comes when:

* Debt is taken on unconsciously (you don't realize you're creating problems)  
* Debt accumulates without being paid down  
* Interest compounds (the debt makes everything slower over time)  
* You can't pay it back (the codebase becomes unmaintainable)

### **The True Cost of Debt** 

While technical debt is defined by future rework, its most dangerous consequence is immobility—the fear of changing the code. As Michael Feathers, author of *Working Effectively with Legacy Code*, famously stated, legacy code is simply "code without tests." Without automated tests, any change becomes a high-risk operation, trapping the codebase in its current, subpar state. This guide is designed to not only find the debt but also to identify the tests needed to create a safety net for future refactoring.

### **Types of Technical Debt**

**Deliberate Debt** \- Conscious trade-offs made for valid reasons:

* "We're hardcoding this configuration to ship the MVP, will make it configurable in v2"  
* "Using a simple algorithm now, will optimize if performance becomes an issue"  
* This is documented, tracked, and planned to be addressed

**Accidental Debt** \- Unintentional problems from lack of knowledge or oversight:

* Not knowing about a better pattern  
* Unaware of the consequences of a choice  
* Missing edge cases or error conditions  
* This should be fixed once discovered

**Bit Rot** \- Code that was good once but hasn't kept up:

* Dependencies fall behind  
* Patterns become outdated  
* New language features offer better approaches  
* This needs periodic maintenance

**Strategic Debt** \- Intentional shortcuts in prototypes or experiments:

* Proof-of-concept code that goes to production  
* Quick validation of an idea  
* This needs to be acknowledged and either rewritten or retired

### **Why Assessment Matters**

Regular technical debt assessment helps you:

* **Make informed decisions** \- Understand trade-offs when planning work  
* **Prevent accumulation** \- Catch problems before they compound  
* **Improve velocity** \- Identify what's slowing development  
* **Reduce risk** \- Find security, performance, or reliability issues  
* **Support handoffs** \- Document current state for future teams  
* **Build quality culture** \- Make technical health visible and valued

---

## **Before You Start**

### **Set Clear Goals**

Know why you're assessing. Different goals require different approaches:

**Quick Health Check (20-30 min)**

* Goal: Identify the biggest immediate risks  
* Approach: Core dimensions only, focus on high-priority findings  
* Output: Top 3 concerns, 1-2 quick wins

**Standard Baseline Assessment (45-60 min)**

* Goal: Understand overall technical health  
* Approach: Core dimensions \+ relevant context-specific  
* Output: Comprehensive findings, prioritized recommendations

**Deep Handoff Preparation (90+ min)**

* Goal: Document everything for incoming team  
* Approach: All relevant dimensions thoroughly  
* Output: Complete technical debt inventory, refactoring roadmap

**Focused Investigation (30-60 min)**

* Goal: Explore specific concern area  
* Approach: Deep dive on 1-2 dimensions  
* Output: Detailed findings and recommendations for that area

### **Gather Context**

Before diving into code, understand:

**Project Background:**

* What does this project do?  
* Who are the users/stakeholders?  
* What stage is it in (prototype, MVP, mature product)?  
* What's the deployment context (production, research, internal tool)?

**Technical Context:**

* What's the tech stack?  
* How old is the codebase?  
* How many active developers?  
* What's the development pace (active daily, occasional maintenance)?

**Historical Context:**

* Why were certain choices made?  
* What constraints existed at the time?  
* What's changed since initial development?  
* Any known problem areas?

**Future Plans:**

* Will this be maintained long-term?  
* Are there plans for significant changes?  
* What's the next team's capability level?  
* What's the expected lifespan?

This context helps you interpret findings fairly and make appropriate recommendations.

### **Prepare Your Environment**

**Get the code running:**

* Clone the repository fresh  
* Follow setup documentation  
* Note any difficulties (these may indicate debt)  
* Run tests if they exist

**Gather tools:**

* Code editor with search capabilities  
* Static analysis tools (linters, security scanners)  
* Code coverage tools  
* Dependency checkers  
* Note-taking system for findings

**Access documentation:**

* README and setup guides  
* Architecture documentation  
* Issue tracker  
* Recent pull requests or commits  
* Any existing technical debt documentation

### **Calibrate Your Expectations**

Remember:

* **Perfect code doesn't exist** \- You're looking for significant debt, not perfection  
* **Context matters** \- What's acceptable for a prototype differs from production  
* **Resources are finite** \- Teams make reasonable trade-offs given constraints  
* **All code has history** \- Understand the "why" before judging  
* **You'll miss things** \- No assessment is exhaustive, focus on what matters most

---

## **Conducting the Assessment**

### **General Assessment Strategy**

**Start Broad, Then Dig Deep:**

1. **Initial scan (5-10 min):**

   * Look at repository structure  
   * Read the README  
   * Check when code was last updated  
   * Look at file sizes (very large files often indicate problems)  
   * Run tests if they exist  
2. **Targeted investigation (20-60 min):**

   * Pick specific dimensions from rubric  
   * Use rubric's "Assessment Questions" as checklist  
   * Document specific findings with locations  
   * Look for patterns across the codebase

3. **Synthesis (10-15 min):**

   * Identify themes and patterns  
   * Prioritize findings  
   * Note quick wins  
   * Prepare recommendations

### **Choosing What to Assess**

**Always Assess (Core Dimensions 1-5):**

* Code Complexity & Clarity  
* Test Coverage & Quality  
* Dependencies & Coupling  
* Error Handling & Resilience  
* Technical Debt Documentation

**Add Based on Project Type:**

| Project Type | Add These Dimensions |
| ----- | ----- |
| Web application | Security, Frontend, API Design |
| Mobile app | Mobile-Specific, Performance |
| Library/SDK | Library/Framework, API Design |
| Data pipeline | Data Management, Performance |
| Embedded/Hardware | Hardware-Dependent, Performance |
| Microservices | API Design, Observability, Build & Deployment |
| Domain-rich app | Domain Modeling, Data Management |
| Research software | Domain Modeling, Documentation |

**Time-Based Priority:**

**20-30 minutes available:**

* Core dimensions 1-3 only  
* Focus on finding the scariest problems  
* Document top concerns with specific locations

**45-60 minutes available:**

* All core dimensions  
* 2-3 most relevant context-specific dimensions  
* Categorize findings by priority  
* Identify patterns

**90+ minutes available:**

* All relevant dimensions  
* Deep investigation of problem areas  
* Comprehensive documentation  
* Detailed recommendations

### **Effective Investigation Techniques**

**File-Based Investigation:**

1\. Identify "hot spots" \- most frequently changed files  
   \- Use: git log \--format=format: \--name-only | sort | uniq \-c | sort \-r  
   \- These files are most likely to have debt that impacts work

2\. Check largest files  
   \- Use: find . \-name "\*.py" \-exec wc \-l {} \+ | sort \-rn | head  
   \- Large files often indicate complexity issues

3\. Find oldest unchanged files  
   \- May indicate stable code OR abandoned code  
   \- Check if they're still relevant

**Pattern-Based Search:**

Useful searches:  
\- TODO, FIXME, HACK, XXX comments  
\- Empty catch/except blocks: try { } catch {}  
\- Magic numbers: \\b\\d+\\b (then filter for constants)  
\- Long lines: \> 120 characters (may indicate complexity)  
\- Nested conditionals: count indentation levels  
\- Duplicated code: look for similar patterns

**Test-Based Investigation:**

1\. Run test suite  
   \- Does it pass?  
   \- How long does it take?  
   \- Any flaky tests?

2\. Check coverage  
   \- What's the overall percentage?  
   \- Which critical paths lack coverage?  
   \- Are there tests for recent bugs?

3\. Read test names  
   \- Do they describe behavior?  
   \- Can you understand what's being tested?

**Dependency Investigation:**

1\. Check versions  
   \- When were dependencies last updated?  
   \- Are there security vulnerabilities?  
   \- Are major versions behind current?

2\. Check necessity  
   \- Are all dependencies actually used?  
   \- Could any be removed?  
   \- Are there duplicates (lodash \+ underscore)?

3\. Check transitive dependencies  
   \- What are you pulling in indirectly?  
   \- Any known problematic transitive deps?

**Guided Tour Method:**

If possible, have someone familiar with the codebase give you a guided tour:

* "Show me the most critical component"  
* "Walk me through a typical user flow"  
* "What's the scariest part of the code?"  
* "What would you fix if you had a week?"  
* "What breaks most often?"

This quickly reveals known pain points and provides context for assessment.

### **Taking Effective Notes**

**Real-Time Documentation:**

As you assess, capture:

\#\# \[Dimension Name\]

\#\#\# Finding: \[Short description\]  
\*\*Location:\*\* src/payments/processor.py:45-67  
\*\*Problem:\*\* \[Specific issue\]  
\*\*Impact:\*\* \[Why it matters\]  
\*\*Evidence:\*\* \[What you observed\]  
\*\*Priority:\*\* \[High/Medium/Low\]

\*\*Example code:\*\*  
\[Paste relevant snippet if helpful\]

\*\*Recommendation:\*\* \[What should be done\]

**Use Consistent Markers:**

* 🔴 HIGH \- Blocks work, poses significant risk  
* ⚠️ MEDIUM \- Slows work, increases maintenance  
* ✅ QUICK WIN \- High impact, low effort  
* 📋 PATTERN \- Recurring issue across codebase

**Capture Context:** Don't just note problems, note:

* Why this matters for this project  
* How widespread the issue is  
* Whether it's getting better or worse  
* Any mitigating factors

### **Common Pitfalls to Avoid**

**Analysis Paralysis:**

* Don't try to document every issue  
* Focus on what matters most  
* Use time constraints to force prioritization

**Perfection Seeking:**

* Not every TODO needs fixing  
* Some debt is acceptable  
* Focus on debt that actively hurts

**Technology Bias:**

* "I wouldn't use this framework" isn't debt  
* Assess the code within its technical choices  
* Don't recommend rewriting in your preferred stack

**Recency Bias:**

* Last modified code gets more attention  
* Don't ignore stable areas that may have debt  
* Balance recent issues with long-standing problems

**Missing the Forest for Trees:**

* Individual issues matter less than patterns  
* 100 small issues may indicate systemic problem  
* Look for root causes, not just symptoms

---

## **Interpreting Findings**

### **Understanding the Ratings**

**✅ Established** doesn't mean perfect:

* Some issues may exist  
* But they're not blocking work  
* Quality is appropriate for context  
* Debt is managed, not absent

**⚠️ Emerging** means manageable:

* Issues present but not critical  
* Awareness exists  
* Could become problematic if ignored  
* Good target for incremental improvement

**🔴 Needs Attention** means action required:

* Actively causing problems  
* Blocking development or creating risk  
* Will get worse without intervention  
* Should be addressed soon

### **Prioritization Framework**

The most common and widely-used approach is the **Impact vs. Effort** matrix—a well-established framework that helps balance what matters most against what's feasible to accomplish:

               	     High Impact  
                    		 |  
         Quick Wins 	 | 	Strategic Priorities  
         (Do Now)    	 | 	(Plan For)  
                     		 |  
     –--------------------------+------------------  
                     		 |  
          Monitor    	 | 	Low Priority  
         (Watch)     	 | 	(Backlog)  
                     		 |  
                	     Low Impact  
            
          		Low Effort → High Effort

**Quick Wins (High Impact, Low Effort):**

* Security vulnerabilities with simple fixes  
* Performance issues with obvious solutions  
* Blocking bugs that are easy to resolve  
* Documentation gaps that take minutes to fill

**Strategic Priorities (High Impact, High Effort):**

* Architectural refactoring  
* Test coverage for complex systems  
* Major dependency upgrades  
* Core algorithm improvements

**Monitor (Low Impact, Low Effort):**

* Style inconsistencies  
* Minor optimizations  
* Small documentation improvements  
* Good tasks for onboarding

**Low Priority (Low Impact, High Effort):**

* Nice-to-have refactorings  
* Premature optimizations  
* Over-engineering prevention

**Why Impact vs. Effort Works:**

* Students and Tech Leads can realistically estimate effort  
* Impact relates directly to project goals (functionality, maintainability, handoff readiness)  
* Creates clear, actionable categories  
* Helps manage limited time and resources in academic contexts  
* Balances idealism with pragmatism

**Alternative Prioritization Dimensions**

Depending on your project context, other dimensions may be more relevant:

**Impact vs. Risk** (for safety-critical or high-stakes systems):

* Focuses on what could break vs. what matters  
* Useful when consequences of failure are severe  
* Good for medical, financial, or infrastructure projects  
* Helps identify "must fix" vs. "should fix"

**Impact vs. Urgency** (for time-sensitive projects):

* Emphasizes time-sensitivity over effort  
* Useful when external deadlines drive priorities  
* Good for projects with fixed delivery dates or compliance requirements  
* Distinguishes "needs to happen by X date" from "eventually"

**Value vs. Complexity** (for product-focused teams):

* Reframes "impact" as business/user value  
* Reframes "effort" as technical complexity  
* Useful when stakeholder value is primary concern  
* Good for client-facing or commercial projects

**Your Own Framework:**

The right prioritization approach depends on:

* **Project constraints**: Time, resources, expertise available  
* **Stakeholder priorities**: What matters most to your users/clients  
* **Risk tolerance**: How much uncertainty can you accept  
* **Team dynamics**: What resonates with your team's working style

**Using Multiple Lenses:**

You don't have to choose just one framework. Consider assessing technical debt through multiple lenses:

1. Start with Impact vs. Effort for initial triage  
2. Re-examine high-impact items through Risk lens for safety considerations  
3. Layer in Urgency for items with external deadlines  
4. Adjust based on team feedback and evolving understanding

The goal is **informed decision-making**, not rigid adherence to any single framework. Use whatever helps you have productive conversations about priorities and make choices you can explain and defend.

### **Identifying Patterns**

Individual issues matter less than patterns. Look for:

**Systemic Issues:**

* Same problem in multiple places  
* Example: N+1 queries in every list view  
* Indicates: Lack of awareness or standards  
* Solution: Education \+ tooling \+ refactoring pattern

**Skill Gaps:**

* Consistent misuse of a technology or pattern  
* Example: No error handling anywhere  
* Indicates: Team doesn't know best practices  
* Solution: Training \+ examples \+ pair programming

**Process Problems:**

* Certain types of issues keep appearing  
* Example: Regressions in tested code  
* Indicates: Process not catching issues  
* Solution: Improve CI/CD, add automated checks

**Architecture Limitations:**

* Workarounds accumulating  
* Example: Many files working around core design flaw  
* Indicates: Fundamental design issue  
* Solution: Architectural refactoring

**Resource Constraints:**

* Evidence of rushing or shortcuts  
* Example: Many TODOs, minimal tests  
* Indicates: Not enough time for quality  
* Solution: Adjust expectations or timeline

### **Contextual Interpretation**

Same finding means different things in different contexts:

**Prototype vs. Production:**

* Prototype: Hardcoded config is fine  
* Production: Hardcoded config is high-priority debt

**Internal Tool vs. Public Product:**

* Internal: Basic error messages okay  
* Public: Error messages need to be user-friendly

**Learning Project vs. Mature Product:**

* Learning: Some experimental code expected  
* Mature: Experimental code should be cleaned up

**Single Developer vs. Large Team:**

* Single: Less need for extensive documentation  
* Team: Documentation critical for coordination

**Short-Lived vs. Long-Term:**

* Short-lived: Many shortcuts acceptable  
* Long-term: Shortcuts become expensive

Always interpret findings within project context.

---

## **Common Patterns and What They Mean**

### **"Everything is a Service" (Anemic Domain Model)**

**Pattern:**

* Domain objects are just data containers  
* All logic in service classes  
* Getters and setters everywhere

**What it means:**

* Team may not understand domain modeling  
* Possible over-application of service pattern  
* Business logic scattered

**Impact:**

* Hard to find where business rules are  
* Easy to create invalid states  
* Testing requires full stack

**When it's okay:**

* Simple CRUD applications  
* Very thin domain layer  
* Data-focused applications

**When to address:**

* Complex business rules  
* Rules duplicated across services  
* Hard to reason about business logic

### **"Works on My Machine"**

**Pattern:**

* Setup is complex and undocumented  
* Different developers have different environments  
* Build failures blamed on environment

**What it means:**

* No standardized development environment  
* Build process not automated  
* Documentation gaps

**Impact:**

* Onboarding takes days/weeks  
* Lost productivity  
* "Environment" becomes excuse

**Solutions:**

* Document setup thoroughly  
* Use containerization (Docker)  
* Automate environment setup  
* Make build reproducible

### **"The Big Ball of Mud"**

**Pattern:**

* No clear architecture  
* Everything depends on everything  
* Hard to understand system structure

**What it means:**

* Organic growth without design  
* No architectural guidance  
* Technical debt accumulated

**Impact:**

* Changes ripple unpredictably  
* Hard to test  
* New features take longer over time

**Solutions:**

* Extract clear modules  
* Define interfaces  
* Refactor toward architecture  
* May need significant restructuring

### **"Comment-Driven Development"**

**Pattern:**

* Lots of commented-out code  
* Comments explain what code does (not why)  
* TODOs everywhere without tracking

**What it means:**

* Fear of deleting code  
* Code unclear enough to need explanation  
* Technical debt not managed

**Impact:**

* Code is cluttered  
* Unclear what's active  
* Debt invisible

**Solutions:**

* Delete commented code (it's in git)  
* Improve code clarity  
* Track TODOs in issues  
* Regular cleanup

### **"The God Object"**

**Pattern:**

* One class/module does many things  
* Hundreds or thousands of lines  
* Everything depends on it

**What it means:**

* Single Responsibility Principle violated  
* Accumulated responsibilities over time  
* Lack of refactoring

**Impact:**

* Hard to understand  
* Hard to test  
* Changes risky

**Solutions:**

* Extract smaller classes  
* Separate concerns  
* Define clear responsibilities  
* Gradual refactoring

### **"Test Decay"**

**Pattern:**

* Tests exist but are ignored  
* Many tests disabled  
* Tests consistently fail

**What it means:**

* Tests became burden, not help  
* Often due to poor test design  
* Lost confidence in test suite

**Impact:**

* No safety net for changes  
* Quality declining  
* Team stops writing tests

**Solutions:**

* Fix or delete broken tests  
* Improve test quality  
* Make tests fast and reliable  
* Rebuild confidence gradually

### **"Dependency Hell"**

**Pattern:**

* Many outdated dependencies  
* Conflicting version requirements  
* Security vulnerabilities

**What it means:**

* Dependencies not maintained  
* Fear of updating  
* Lack of process

**Impact:**

* Security risks  
* Missing bug fixes  
* Harder to update over time

**Solutions:**

* Regular dependency updates  
* Automated vulnerability scanning  
* Update in small increments  
* Test after updates

---

## **Communicating Results**

### **Tailoring to Your Audience**

**For Your Team:**

* Focus on actionable items  
* Connect to current pain points  
* Emphasize quick wins  
* Frame as opportunities to improve

**For Stakeholders/Clients:**

* Explain business impact  
* Quantify where possible (time, risk, cost)  
* Prioritize clearly  
* Provide options and recommendations

**For Incoming Teams:**

* Comprehensive documentation  
* Known issues and workarounds  
* Context about decisions  
* Clear priorities for starting work

**For Yourself (Documentation):**

* Detailed findings for future reference  
* Patterns and root causes  
* Learning for next project  
* Track progress over time

### **Effective Report Structure**

\# Technical Debt Assessment: \[Project Name\]

\#\# Executive Summary (2-3 paragraphs)  
\- Overall health assessment  
\- Top 3 priorities  
\- Key recommendations

\#\# Context  
\- Project overview  
\- Assessment scope and approach  
\- Date and assessor

\#\# Key Findings

\#\#\# High Priority Issues  
For each:  
\- Clear description  
\- Specific location  
\- Impact explanation  
\- Recommended action

\#\#\# Medium Priority Issues  
\[Same structure\]

\#\#\# Quick Wins  
\[Same structure\]

\#\#\# Patterns Observed  
\- Systemic issues  
\- Root causes  
\- Recommendations

\#\# Detailed Findings by Dimension  
\[For comprehensive assessments\]

\#\#\# \[Dimension Name\]  
\- Rating and rationale  
\- Specific findings  
\- Evidence and examples  
\- Recommendations

\#\# Recommendations

\#\#\# Immediate Actions (This Sprint)  
\#\#\# Short-term (Next 1-2 Sprints)  
\#\#\# Long-term (Future Planning)

\#\# Positive Observations  
\- What's working well  
\- Good practices to maintain  
\- Strengths to build on

\#\# Appendices  
\- Full findings list  
\- Code examples  
\- References and resources

### **Writing Effective Findings**

**Be Specific:**

Bad:  "Code quality issues"  
Good: "8 functions exceed 100 lines in src/orders/,   
       making them hard to test and maintain"

Bad:  "Poor test coverage"  
Good: "Payment processing (src/payments/) has 0% test coverage.  
       This is our most critical business logic and handles  
       real money. Bugs here directly impact revenue."

**Focus on Impact:**

Bad:  "Uses old version of library"  
Good: "Using library v2.3 from 2019\. Current is v4.1.  
       Missing: security patches for 3 CVEs, performance  
       improvements, and bug fixes. Updating would reduce  
       latency by \~40% according to library benchmarks."

**Provide Context:**

Bad:  "Functions are too long"  
Good: "Functions exceed 100 lines in frequently-modified files.  
       This happened because team was under tight deadlines  
       during initial development. Now that pace has slowed,  
       worth refactoring to improve maintainability."

**Be Constructive:**

Bad:  "The code is a mess and needs to be rewritten"  
Good: "The codebase has accumulated debt over rapid development.  
       Key issues: X, Y, Z. Recommend targeted refactoring  
       starting with highest-pain areas. Rewriting not necessary \-  
       incremental improvement is feasible."

**Balance Criticism with Recognition:**

"While test coverage is low (20%), the tests that exist  
are well-structured and reliable. They follow good patterns  
and serve as examples for expanding coverage. The challenge  
is quantity, not quality."

### **Presenting Findings Verbally**

**In Team Meetings:**

1. Start with context and goals of assessment  
2. Acknowledge constraints team faced  
3. Present 2-3 highest priorities  
4. Discuss quick wins  
5. Ask for team input on priorities  
6. End with concrete next steps

**In One-on-Ones:**

* Focus on areas relevant to that person  
* Frame as learning opportunities  
* Ask for their perspective  
* Provide specific, actionable feedback  
* Connect to their growth goals

**In Client Meetings:**

* Lead with business impact  
* Use analogies they'll understand  
* Provide options, not just problems  
* Show you understand their constraints  
* Be clear about risks vs. nice-to-haves

### **Handling Pushback**

**"We didn't have time for quality"**

* Acknowledge real constraints  
* Explain compounding cost  
* Show how debt slows future work  
* Propose sustainable balance

**"That's how \[technology\] works"**

* Distinguish between tool limitations and our code  
* Show examples of better approaches  
* Offer to research best practices  
* May need to verify your understanding

**"It works, why fix it?"**

* Explain preventive maintenance concept  
* Quantify current costs (bugs, slow development)  
* Show trajectory if not addressed  
* Differentiate working from maintainable

**"We'll fix it in the rewrite"**

* Discuss whether rewrite is realistic  
* Show how to improve incrementally  
* Explain rewrite risks  
* Propose middle ground

---

## **Acting on Findings**

### **Creating an Action Plan**

**Immediate Actions (This Sprint):**

* Critical security vulnerabilities  
* Blocking bugs  
* Quick wins that unblock work

**Short-term (1-2 Sprints):**

* High-priority technical debt  
* Systematic issues affecting velocity  
* Foundation for future work

**Long-term (Backlog):**

* Architectural improvements  
* Nice-to-have refactorings  
* Technical debt that's stable

**Ongoing:**

* Dependency updates  
* Documentation maintenance  
* Test coverage improvements  
* Code review standards

### **Integrating with Development Work**

**The 20% Rule:** Allocate \~20% of sprint capacity to technical debt:

* Prevents accumulation  
* Maintains sustainable pace  
* Keeps codebase healthy

**Opportunistic Refactoring:**

* Fix debt when touching nearby code  
* "Leave it better than you found it"  
* Small improvements add up  
* Part of every feature's "definition of done"

**Dedicated Tech Debt Sprints:**

* Occasional focused cleanup  
* Good before major features  
* After rapid development periods  
* Before handoffs

**Spike Stories:**

* Investigate complex technical debt  
* Prototype solutions  
* Estimate refactoring work  
* Make informed decisions

### **Preventing Future Debt**

**Establish Standards:**

* Coding conventions documented  
* Automated linting/formatting  
* Clear definition of done  
* Code review checklist

**Build Quality In:**

* Tests required for features  
* Security checks in CI/CD  
* Performance budgets  
* Documentation updates with code

**Regular Maintenance:**

* Scheduled dependency updates  
* Periodic debt assessment  
* Architecture reviews  
* Refactoring time allocated

**Knowledge Sharing:**

* Pair programming  
* Code review discussions  
* Tech talks on patterns  
* Document "why" decisions

**Make Debt Visible:**

* Track in issue tracker  
* Discuss in retros  
* Include in planning  
* Report to stakeholders

### **Measuring Progress**

**Quantitative Metrics:**

* Test coverage trends  
* Dependency update frequency  
* Build time trends  
* Code complexity metrics  
* Issue close rates

**Qualitative Indicators:**

* Team velocity trending up  
* Fewer production incidents  
* Easier onboarding  
* More confident deployments  
* Better team morale

**Regular Reassessment:**

* Quarterly health checks  
* Before major milestones  
* After significant changes  
* During handoffs

---

## **Special Scenarios**

### **Assessing an Unfamiliar Technology Stack**

**When you don't know the technology:**

1. **Learn the basics:**

   * Read official "best practices" docs  
   * Find style guides for the language/framework  
   * Look up common pitfalls  
   * Identify standard project structure  
2. **Focus on universal principles:**

   * Code complexity (works in any language)  
   * Test coverage (every ecosystem has testing)  
   * Documentation (always relevant)  
   * Dependencies (check for updates)  
3. **Consult experts:**

   * Find developers familiar with stack  
   * Ask specific questions  
   * Get them to review findings  
   * Learn from their perspective  
4. **Be humble:**

   * Acknowledge your limitations  
   * Don't claim expertise you lack  
   * Focus on observable facts  
   * Ask questions, don't assume

### **Assessing Your Own Code**

**Challenges:**

* Blind spots from familiarity  
* Emotional attachment to decisions  
* Knowledge of context that others lack

**Strategies:**

* Imagine explaining code to new team member  
* What would you change if starting over?  
* What breaks most often?  
* What slows you down?  
* Get external review

**Benefits:**

* Identify debt before handoff  
* Document decisions while context is fresh  
* Plan improvements  
* Show stakeholders technical health

### **Assessing Research/Academic Code**

**Special Considerations:**

* Often experimental or exploratory  
* May prioritize correctness over maintainability  
* Might be single-use or throwaway  
* Documentation may focus on methodology

**Adjusted Expectations:**

* Lower emphasis on production-readiness  
* Higher emphasis on reproducibility  
* Documentation of methodology critical  
* Data management and provenance important

**Key Questions:**

* Is the science sound?  
* Can results be reproduced?  
* Is methodology documented?  
* Can others build on this?  
* If productionizing, what would change?

### **Assessing Legacy Code**

**Definition:** Code without tests, or code you're afraid to change.

**Initial Assessment:**

* What does it do? (May not be obvious)  
* What depends on it?  
* How often does it change?  
* What's the risk of not changing it?

**Characterization Testing:**

* Write tests that capture current behavior  
* Even if behavior is buggy  
* Creates safety net for refactoring  
* See Michael Feathers' "Working Effectively with Legacy Code"

**Strangler Fig Pattern:**

* Don't rewrite everything  
* Extract and replace incrementally  
* New code alongside old  
* Gradual migration

### **Assessing Inherited Projects**

**First Week Priorities:**

1. Get it running  
2. Understand what it does  
3. Find the tests (or lack thereof)  
4. Identify the "hot spots" (frequently changed code)  
5. Talk to previous team if possible

**Documentation to Create:**

* Setup instructions (you just did it fresh)  
* Architecture overview  
* Known issues and workarounds  
* Questions for previous team

**Quick Wins:**

* Fix annoying small issues  
* Improve documentation  
* Add missing tests for critical paths  
* Clean up obvious messes

**Build Understanding Before Judging:**

* There may be good reasons for weird decisions  
* Context may have been different  
* Constraints may not be obvious  
* Ask "why?" before "what were they thinking?"

---

## **Tools and Automation**

### **Static Analysis Tools**

**Linters (Code Style and Simple Issues):**

* Python: pylint, flake8, black  
* JavaScript: ESLint, Prettier  
* Java: Checkstyle, PMD  
* Ruby: RuboCop  
* Go: golint, gofmt

**Security Scanners:**

* Dependency checking: npm audit, pip-audit, OWASP Dependency-Check  
* Code scanning: Bandit (Python), Brakeman (Rails), Semgrep  
* Container scanning: Trivy, Clair  
* Secret detection: git-secrets, TruffleHog

**Code Quality Tools:**

* SonarQube: Multi-language quality and security  
* CodeClimate: Code quality metrics  
* Codacy: Automated code reviews  
* DeepSource: Continuous analysis

**Complexity Metrics:**

* Cyclomatic complexity: radon (Python), complexity-report (JS)  
* Code churn: git-based analysis  
* Duplication detection: jscpd, PMD CPD

**Dependency Management:**

* Dependabot: Automated dependency updates  
* Renovate: Dependency automation  
* Snyk: Vulnerability scanning

### **Test Coverage Tools**

* Python: coverage.py, pytest-cov  
* JavaScript: Istanbul, Jest coverage  
* Java: JaCoCo, Cobertura  
* Ruby: SimpleCov  
* .NET: coverlet

### **Documentation Tools**

* API docs: Swagger/OpenAPI, JSDoc, Sphinx  
* Architecture: draw.io, Mermaid, PlantUML  
* ADRs: adr-tools  
* Code documentation: Doxygen, Rustdoc

### **Using Tools Effectively**

**Don't Let Tools Replace Thinking:**

* Tools find symptoms, not root causes  
* 100% coverage doesn't mean good tests  
* Zero linter warnings doesn't mean quality code  
* Tools miss context and patterns

**Integrate into Workflow:**

* Run in CI/CD automatically  
* Block merges on security issues  
* Make reports visible  
* But don't let tools slow development

**Start Small:**

* Don't enable all rules at once  
* Fix or disable noisy rules  
* Gradually increase standards  
* Get team buy-in

**Complement with Manual Review:**

* Tools \+ human review \= best results  
* Tools catch mechanical issues  
* Humans catch design issues  
* Both are necessary

### **Reading the Output of These Tools**

Automated tools produce confident output in consistent units, and confident  
output in consistent units reads as objective whether or not it is. Knowing  
what a given finding is evidence *of* is a skill in its own right:

* A maintainability grade is a vendor's model rendered as a letter  
* A code smell is a rule somebody wrote down  
* Coverage counts lines executed, not behaviour verified  
* A green check means only that the checks which ran, passed  
* But: a tool's silence is not evidence that a practice is absent

The companion guidebook **"Code Futures: Reading Automated Analysis of Your  
Codebase"** (https://github.com/oss-slu/guidebook_code_futures) treats this at  
length, including a worked example report. It is the automated counterpart to  
this document: broad, cheap, repeatable, and blind to everything requiring  
judgement. This guidebook and its rubric remain the assessment instrument — the  
automated report produces evidence to bring to an assessment, not a substitute  
for one.

---

## **Learning Resources**

### **Books**

**On Technical Debt and Quality:**

* "Working Effectively with Legacy Code" \- Michael Feathers  
* "Refactoring: Improving the Design of Existing Code" \- Martin Fowler  
* "Clean Code" \- Robert C. Martin  
* "The Pragmatic Programmer" \- David Thomas & Andrew Hunt

**On Software Engineering Practices:**

* "Modern Software Engineering" \- Dave Farley  
* "Continuous Delivery" \- Jez Humble & Dave Farley  
* "Software Engineering at Google" \- Titus Winters, Tom Manshreck, Hyrum Wright

**On Design and Architecture:**

* "Domain-Driven Design" \- Eric Evans  
* "A Philosophy of Software Design" \- John Ousterhout  
* "Building Microservices" \- Sam Newman  
* "Designing Data-Intensive Applications" \- Martin Kleppmann

**On Testing:**

* "Test-Driven Development By Example" \- Kent Beck  
* "Growing Object-Oriented Software, Guided by Tests" \- Steve Freeman & Nat Pryce

### **Online Resources**

**Blogs and Articles:**

* Martin Fowler's website: martinfowler.com  
* Dave Farley's Continuous Delivery YouTube channel  
* The Pragmatic Engineer blog  
* Joel Spolsky's "Joel on Software"

**Communities:**

* Stack Overflow (for specific questions)  
* Reddit: r/ExperiencedDevs, r/programming  
* Dev.to community  
* Language-specific communities

**Standards and Guidelines:**

* OWASP (security): owasp.org  
* 12 Factor App: 12factor.net  
* Google Style Guides: google.github.io/styleguide  
* Semantic Versioning: semver.org

### **Training and Practice**

**Kata and Exercises:**

* Refactoring exercises  
* Code smell identification  
* Legacy code practice  
* Test-first development

**Code Review:**

* Review others' code regularly  
* Get your code reviewed  
* Learn from feedback  
* Develop evaluation eye

**Pair Programming:**

* Work with more experienced developers  
* Explain your code to others  
* See different approaches  
* Share knowledge

---

## **Assessment Checklist**

Use this checklist for quick reference:

### **Before Assessment**

* \[ \] Understand project context and goals  
* \[ \] Know why you're assessing (quick/standard/deep)  
* \[ \] Clone repository fresh  
* \[ \] Get code running  
* \[ \] Read existing documentation  
* \[ \] Gather necessary tools

### **During Assessment**

* \[ \] Start with broad scan  
* \[ \] Choose relevant dimensions from rubric  
* \[ \] Take specific, located notes  
* \[ \] Look for patterns, not just individual issues  
* \[ \] Consider context when interpreting  
* \[ \] Document evidence for findings

### **Core Dimensions**

* \[ \] Code Complexity & Clarity  
* \[ \] Test Coverage & Quality  
* \[ \] Dependencies & Coupling  
* \[ \] Error Handling & Resilience  
* \[ \] Technical Debt Documentation

### **Context-Specific (as relevant)**

* \[ \] Security Practices  
* \[ \] Performance & Scalability  
* \[ \] Data Management  
* \[ \] API Design & Contracts  
* \[ \] Domain Modeling & Business Logic  
* \[ \] Build & Deployment  
* \[ \] Frontend-Specific  
* \[ \] Mobile-Specific  
* \[ \] Library/Framework-Specific  
* \[ \] Hardware-Dependent  
* \[ \] Observability & Operations

### **Synthesis**

* \[ \] Identify high-priority issues  
* \[ \] Find medium-priority issues  
* \[ \] Note quick wins  
* \[ \] Observe patterns  
* \[ \] Prioritize recommendations

### **Communication**

* \[ \] Tailor to audience  
* \[ \] Be specific with findings  
* \[ \] Focus on impact  
* \[ \] Provide context  
* \[ \] Be constructive  
* \[ \] Balance criticism with recognition

### **Follow-up**

* \[ \] Create action plan  
* \[ \] Track findings in issue tracker  
* \[ \] Schedule follow-up assessment  
* \[ \] Share learning with team

---

## **Frequently Asked Questions**

**Q: How often should I assess technical debt?**

A: Depends on project phase:

* Active development: Every 2-3 sprints  
* Maintenance mode: Quarterly  
* Before major milestones: Always  
* During handoffs: Required  
* When velocity slows: Investigate

**Q: Should I assess during development or after?**

A: Both\!

* During: Continuous awareness, address as you go  
* After sprint: Reflection and planning  
* Before handoff: Comprehensive documentation

**Q: What if I find overwhelming amounts of debt?**

A:

* Don't panic \- this is common  
* Prioritize ruthlessly  
* Start with quick wins  
* Address highest-impact issues first  
* Incremental improvement is okay  
* Consider: is a rewrite actually needed? (Usually not)

**Q: How do I convince stakeholders debt matters?**

A:

* Connect to business impact  
* Quantify velocity slowdown  
* Show risk (security, bugs, outages)  
* Demonstrate compounding costs  
* Propose manageable solution  
* Show ROI of addressing debt

**Q: What if previous team gets defensive?**

A:

* Frame as system assessment, not personal critique  
* Acknowledge constraints they faced  
* Focus on moving forward, not blame  
* Ask for their input on priorities  
* They probably know the problems already  
* You're documenting, not discovering

**Q: Can I use AI tools for assessment?**

A:

* AI can help identify patterns  
* Good for initial scan  
* But lacks context understanding  
* Can't assess "why" behind decisions  
* Use as supplement, not replacement  
* Verify AI findings manually

**Q: What's the difference between technical debt and bugs?**

A:

* Bugs: Code doesn't work as intended  
* Technical debt: Code works but is hard to maintain  
* Bugs need fixing now  
* Debt can often be deferred (but shouldn't be forever)  
* Sometimes debt leads to bugs

**Q: Should I fix debt or build features?**

A:

* False dichotomy \- need both  
* 80/20 rule: 80% features, 20% debt  
* Opportunistic refactoring: fix while in area  
* Critical debt can block features  
* Balance based on context

**Q: How do I assess without seeming critical?**

A:

* Lead with curiosity: "Help me understand why..."  
* Acknowledge trade-offs: "Given X constraint, this makes sense..."  
* Focus on systems, not people: "The codebase has..." not "You wrote..."  
* Collaborative stance: "How can we improve...?"  
* Recognize good decisions too

---

## **Conclusion**

The dimensions you've just measured—from test quality to deployment simplicity—are the very same practices that, as documented in *Accelerate*, distinguish high-performing teams from low-performing ones. This assessment is your first step in building a faster, more stable, and more enjoyable project for all contributors.

Effective technical debt assessment is both an art and a science. The rubric provides structure, but your judgment, context awareness, and communication skills bring the assessment to life.

**Remember:**

**Be Systematic** \- Use the rubric to ensure thorough coverage, but adapt to your context.

**Be Specific** \- Vague findings don't lead to action. Point to specific code, explain specific impact.

**Be Balanced** \- Acknowledge both problems and strengths. Recognize constraints teams faced.

**Be Constructive** \- Frame findings as improvement opportunities, not failures. Provide actionable recommendations.

**Be Realistic** \- Perfect code doesn't exist. Focus on debt that matters. Incremental improvement is progress.

**Be Continuous** \- Assessment isn't a one-time activity. Regular evaluation maintains code health.

The goal isn't to eliminate all technical debt—it's to manage it consciously, address it strategically, and prevent it from undermining your project's success.

Good luck with your assessments\!

### **From Assessment to Actionable Insight**

Having completed this assessment, you have accomplished far more than just "grading" a codebase. The true value of this exercise lies in the learning outcomes that directly support your role as a maintainer and tech lead.

By moving through this guide and rubric, you have learned to:

1. **Systematically Prepare and Diagnose:** You've learned how to **prepare for and conduct an assessment** by scoping it (a quick check vs. a deep audit). More importantly, you've practiced concrete **techniques for identifying technical debt**, making it the star of your investigation. You've learned to:  
   * **Go beyond just reading:** You've used the *Guided Tour Method* to ask a team where problems hide.  
   * **Follow the code:** You've practiced *Code Investigation* by tracing features through the codebase to find hotspots and coupling.  
   * **Use automated tools:** You've seen how *Static Analysis* (like linters or `npm audit`) can instantly reveal dependency and quality issues.  
   * **Translate a 'feeling' into a 'finding':** By using the rubric, you can now connect a vague feeling ("this is messy") to an evidence-based *diagnosis* ("our 'Dependencies & Coupling' dimension needs attention").  
2. **Pragmatically Prioritize Findings:** By using the Impact vs. Effort matrix, you've practiced the essential skill of making strategic trade-offs. You now know **how to prioritize findings**, learning that not all debt is created equal and that your limited time—a critical resource in any academic or open-source project—is best spent on what matters most.  
3. **Communicate Results Effectively:** The most important outcome is that **the output of your assessment is not a score; it's a plan.** You have created a prioritized, living artifact that serves as a powerful tool to **communicate results effectively**, building a shared understanding with your team and creating an honest, transparent "map" for new contributors.  
4. **Develop Strategies for Addressing Debt:** This prioritized plan *is* the foundation for your strategy. You can now decide *how* to address the debt: integrating "Strategic Priorities" into your long-term roadmap, using the "Monitor" quadrant for good onboarding tasks, and tackling "Quick Wins" to build momentum. You've learned to move from simply *finding* debt to developing **strategies for actively managing it**.

You are now equipped not only to *identify* debt but to *manage* it. This process is the core of sustainable software development and effective technical leadership.

---

## **Acknowledgements**

These technical debt assessment materials were developed collaboratively with Gemini (Google AI) and Claude (Anthropic AI) to support Tech Leads in the Opening Skills & Solutions program at Saint Louis University. The rubric and guidance document synthesize established software engineering principles with practical assessment approaches tailored for academic open source program offices.

The frameworks draw on best practices from industry leaders including Martin Fowler, Dave Farley, Eric Evans, and others cited throughout, adapted for the specific context of student-led open source development teams.

---

## **Appendix: Quick Reference Cards**

### **20-Minute Assessment Card**

GOAL: Find biggest risks

1\. SCAN (5 min)  
   \- Run tests \- pass? How long?  
   \- Check dependency versions  
   \- Look at file sizes  
   \- Spot check 2-3 critical files

2\. CORE CHECK (12 min)  
   \- Complexity: Functions \>100 lines? Deep nesting?  
   \- Tests: Coverage on critical paths?  
   \- Dependencies: Any red flags?

3\. DOCUMENT (3 min)  
   \- Top 3 concerns with locations  
   \- 1-2 quick wins  
   \- One-sentence summary

OUTPUT: Brief findings, immediate actions

### **60-Minute Assessment Card**

GOAL: Understand overall health

1\. PREPARE (5 min)  
   \- Get code running  
   \- Review recent changes  
   \- Identify critical areas

2\. CORE DIMENSIONS (25 min)  
   \- All 5 core dimensions  
   \- Use rubric assessment questions  
   \- Document specific findings

3\. CONTEXT-SPECIFIC (20 min)  
   \- Pick 2-3 relevant dimensions  
   \- Deep dive on project-specific concerns  
   \- Look for patterns

4\. SYNTHESIZE (10 min)  
   \- Categorize: High/Medium/Low priority  
   \- Identify quick wins  
   \- Note patterns  
   \- Draft recommendations

OUTPUT: Comprehensive findings, prioritized actions

### **Pattern Recognition Card**

LOOK FOR THESE PATTERNS:

□ Same problem in multiple places  
  → Systemic issue, needs broad solution

□ Workarounds accumulating  
  → Core design problem

□ Recent fixes in same area  
  → Fragile code or misunderstood requirements

□ Many TODOs about same thing  
  → Known issue not being addressed

□ Tests for same functionality keep breaking  
  → Brittle tests or unclear requirements

□ Similar code in multiple files  
  → Missing abstraction

□ Many comments explaining code  
  → Code not self-documenting

PATTERNS REVEAL ROOT CAUSES  
Fix patterns, not just symptoms

---

*This guidance document is designed to be used alongside the Codebase Technical Assessment Rubric. Together, they provide a comprehensive framework for understanding and improving code quality.*

