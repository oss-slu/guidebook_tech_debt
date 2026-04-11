# **Codebase Technical Assessment Rubric**

## **How to Use This Rubric**

This rubric helps evaluate technical debt across multiple dimensions. Use it for:

* Initial baseline assessment of a codebase  
* Regular health checks during development  
* Handoff preparation and documentation  
* Self-assessment by maintainer teams  
* External evaluation by mentors or reviewers

**Assessment Scale:**

* ✅ **Established** \- Healthy state, minimal technical debt in this area  
* ⚠️ **Emerging** \- Some debt present, manageable but needs attention  
* 🔴 **Needs Attention** \- Significant debt that impacts development or poses risk

**Time Investment Options:**

* **Quick scan (20-30 min):** Focus on Core Dimensions 1-3 only  
* **Standard assessment (45-60 min):** Core Dimensions \+ 2-3 relevant Context-Specific Dimensions  
* **Deep audit (90+ min):** All dimensions with detailed findings

---

## **Core Dimensions (assess for all projects)**

The following five dimensions apply to virtually all software projects and provide a foundation for understanding technical debt. Assess these first in any evaluation.

**Note for Open Source Projects:** If your project seeks external contributions or involves rotating team members (common in academic settings), **Developer Experience & Contributor Onboarding** (dimension \#6) should be treated as essential and assessed alongside core dimensions.

**Note:** For projects with complex business logic, domain-rich applications, or systems modeling real-world processes (such as in much of research software), also consider the Context-Specific dimension **"Domain Modeling & Business Logic"** (dimension \#11) which evaluates how well code reflects domain concepts and business rules.

### **All Dimensions Overview**

**Core Dimensions** (assess for all projects)

1. Code Complexity & Clarity  
2. Test Coverage & Quality  
3. Dependencies & Coupling  
4. Error Handling & Resilience  
5. Technical Debt Documentation

**Context-Specific Dimensions** (assess as relevant) 

6. **Developer Experience & Contributor Onboarding**  
7. Security Practices   
8. Performance & Scalability   
9. Data Management   
10. API Design & Contracts   
11. **Domain Modeling & Business Logic**   
12. Build & Deployment   
13. Frontend-Specific   
14. Mobile-Specific   
15. Library/Framework-Specific   
16. Hardware-Dependent Projects   
17. Observability & Operations

---

### **1\. Code Complexity & Clarity**

Evaluates whether code is understandable, maintainable, and appropriately structured.

| Rating | Summary |
| ----- | ----- |
| ✅ **Established** | Functions are focused and appropriately sized, nesting is shallow, names are descriptive, complex logic is explained |
| ⚠️ **Emerging** | Mix of clear and complex code, some functions too large, occasional deep nesting, inconsistent naming |
| 🔴 **Needs Attention** | Many long/complex functions, deep nesting common, cryptic naming, "what does this do?" is frequent |

#### **What Good Looks Like**

**Function Size and Focus** (Martin Fowler's "Extract Method" principle):

* Functions typically 5-30 lines, rarely exceeding 50  
* Each function does one thing at one level of abstraction  
* Function names clearly describe what they do  
* Easy to understand without reading the implementation

Example (Python):

def calculate\_order\_total(order):  
    """Calculate total with tax and shipping."""  
    subtotal \= sum\_line\_items(order.items)  
    tax \= calculate\_tax(subtotal, order.tax\_rate)  
    shipping \= calculate\_shipping(order.weight, order.destination)  
    return subtotal \+ tax \+ shipping

**Appropriate Abstraction Levels**:

* High-level functions compose lower-level operations  
* Implementation details hidden behind clear interfaces  
* No mixing of business logic with infrastructure concerns

**Clear Naming**:

* Names reveal intent: `calculate_tax()` not `calc_t()`  
* Boolean variables read naturally: `is_valid`, `has_permission`, `should_retry`  
* Avoid abbreviations unless universally understood in domain

#### **Code Smells to Look For**

**Long Methods** (Martin Fowler, "Refactoring"):

* Functions exceeding 50 lines often do too much  
* Particularly problematic: \>100 line functions with multiple concerns  
* Sign: You need a comment to explain what a section does (extract that section\!)

**Deep Nesting** (Cyclomatic Complexity):

* Nesting beyond 3 levels makes code hard to reason about  
* Guard clauses reduce nesting: return early for edge cases  
* Consider extracting nested blocks into named functions

Bad:

function processOrder(order) {  
    if (order) {  
        if (order.items.length \> 0\) {  
            if (order.customer.isValid) {  
                if (order.paymentMethod) {  
                    if (order.paymentMethod.isAuthorized) {  
                        // actual processing here, 5 levels deep  
                    }  
                }  
            }  
        }  
    }  
}

Better:

function processOrder(order) {  
    if (\!order || order.items.length \=== 0\) return;  
    if (\!order.customer.isValid) throw new InvalidCustomerError();  
    if (\!order.paymentMethod?.isAuthorized) throw new PaymentError();  
      
    // actual processing here, at top level  
}

**Magic Numbers and Strings**:

* Unexplained constants scattered through code  
* Configuration values hardcoded in logic  
* Should be named constants with clear meaning

Bad: `if (status === 3 && age > 21)` Good: `if (status === ORDER_COMPLETED && age > LEGAL_DRINKING_AGE)`

**Primitive Obsession**:

* Using primitives where domain objects would clarify  
* Example: passing separate `street, city, state, zip` instead of `Address` object  
* Makes refactoring harder and obscures relationships

**Cryptic Names**:

* Single letters outside loop counters: `a`, `x`, `tmp`  
* Unexplained abbreviations: `prc_ord_w_tx()`  
* Generic names that don't convey purpose: `data`, `info`, `handle`

#### **Assessment Questions**

* Pick 3 of the most frequently modified files. Can you understand what they do in \<2 minutes each?  
* Find the longest function. Could you explain what it does to a teammate?  
* Look for conditional blocks. How deep do they nest? Are there many at 4+ levels?  
* Search for numbers in the code (regex: `\b\d+\b`). Are they named constants?  
* Pick a function name. Does it accurately describe what the function does?

---

### **2\. Test Coverage & Quality**

Evaluates whether code is testable and adequately tested. This dimension assesses how reliably the code can be changed without introducing new bugs. We are not merely looking for a coverage number; we are looking for evidence of a safety net. Following the principles laid out in *Working Effectively with Legacy Code*, the highest form of technical debt is code that is difficult or impossible to put under test. If you cannot safely refactor a section of code, that section is effectively frozen by debt. The quality of your tests—their clarity, isolation, and speed—is the primary measure of a codebase's *refactorability*. Testing is a key element of any software that is involved in research, as it is the sort of software engineering practice that enables what Wilson et al. refer to as “reproducible research”. 

| Rating | Summary |
| ----- | ----- |
| ✅ **Established** | Critical paths tested, tests reliable and maintainable, edge cases covered, tests serve as documentation |
| ⚠️ **Emerging** | Basic happy-path testing, some critical paths lack tests, occasional flaky tests, edge cases mostly untested |
| 🔴 **Needs Attention** | Minimal/no automated testing, critical functionality untested, tests frequently ignored or fail |

#### **What Good Looks Like**

**Test Coverage Philosophy** (Dave Farley, "Modern Software Engineering"):

* Not about 100% coverage, but about covering what matters  
* Critical business logic fully tested  
* Integration points have tests  
* Edge cases and error conditions tested  
* Tests enable confident refactoring

**Test Quality Characteristics**:

* **Fast**: Test suite runs in seconds/minutes, not hours  
* **Isolated**: Tests don't depend on each other or external state  
* **Repeatable**: Same results every time  
* **Self-validating**: Clear pass/fail, no manual verification  
* **Timely**: Written alongside or before production code

**Effective Test Structure** (Arrange-Act-Assert pattern):

def test\_order\_total\_includes\_tax():  
    \# Arrange: Set up test data  
    order \= Order(items=\[Item(price=100)\], tax\_rate=0.08)  
      
    \# Act: Execute the behavior  
    total \= calculate\_order\_total(order)  
      
    \# Assert: Verify the outcome  
    assert total \== 108.0

**Tests as Documentation**:

* Test names describe behavior: `test_discount_not_applied_to_sale_items`  
* Reading tests teaches you how the system works  
* Tests show examples of actual usage

#### **Code Smells to Look For**

**Untested Critical Paths**:

* Payment processing without tests  
* Authentication/authorization logic untested  
* Data transformation or business rules without coverage  
* "We'll add tests later" (they never get added)

**Flaky Tests**:

* Tests that pass/fail randomly  
* Often due to timing issues, shared state, or external dependencies  
* Team starts ignoring test failures because "they're always flaky"  
* Sign of poor test design or insufficient isolation

**Test Fragility** (Martin Fowler, "Test Smells"):

* Tests break when implementation changes even though behavior is same  
* Tests coupled to internal implementation details  
* Change One Thing, Break Many Tests syndrome

**Missing Edge Case Testing**:

* Only happy path tested  
* No tests for: null/empty inputs, boundary values, error conditions  
* No tests for: concurrent access, resource exhaustion, timeouts

**Test Names Don't Explain Intent**:

\# Bad  
def test\_1(): ...  
def test\_order(): ...  
def test\_method(): ...

\# Good  
def test\_order\_total\_includes\_sales\_tax(): ...  
def test\_order\_rejects\_negative\_quantities(): ...  
def test\_order\_applies\_bulk\_discount\_over\_100\_units(): ...

**No Tests for Recently Fixed Bugs**:

* Bug gets fixed but no regression test added  
* Same bug reappears later  
* Missing opportunity to prevent recurrence

**Commented-Out Tests**:

* Tests that were disabled instead of fixed or deleted  
* Often indicates tests that became flaky or hard to maintain  
* Creates confusion about what's actually being tested

#### **Assessment Questions**

* Run the test suite. Does it pass? How long does it take?  
* Check test coverage for your most complex module. What's covered?  
* Identify a recent bug. Would existing tests have caught it?  
* Look at test names. Can you understand what they're testing?  
* Try changing a method signature. How many tests break?  
* Check for tests with `sleep()` or `wait()` \- signs of timing dependencies  
* Are there TODO comments about "add tests later"?

---

### **3\. Dependencies & Coupling**

Evaluates how components relate to each other and external dependencies.

| Rating | Summary |
| ----- | ----- |
| ✅ **Established** | Clear module boundaries, loose coupling, dependencies current and necessary, minimal duplication |
| ⚠️ **Emerging** | Some tight coupling, a few outdated dependencies, occasional circular dependencies, some duplication |
| 🔴 **Needs Attention** | Extensive tight coupling, many outdated dependencies, circular dependencies, significant duplication |

#### **What Good Looks Like**

**Loose Coupling** (Dave Farley, "Continuous Delivery"):

* Modules depend on abstractions/interfaces, not concrete implementations  
* Changes in one module rarely require changes in others  
* Components can be tested independently  
* Clear, narrow interfaces between modules

Example (Dependency Inversion Principle):

\# Bad: Direct dependency on concrete implementation  
class OrderProcessor:  
    def \_\_init\_\_(self):  
        self.emailer \= SMTPEmailer()  \# Tightly coupled\!  
      
    def process(self, order):  
        self.emailer.send(order.customer.email, "Order confirmed")

\# Good: Depend on abstraction  
class OrderProcessor:  
    def \_\_init\_\_(self, notifier: Notifier):  \# Any notifier works  
        self.notifier \= notifier  
      
    def process(self, order):  
        self.notifier.send(order.customer.email, "Order confirmed")

**High Cohesion**:

* Related functionality grouped together  
* Modules have single, well-defined purpose  
* Low "feature envy" (method uses more data from another class than its own)

**Dependency Management**:

* Dependencies are up-to-date (within last 6-12 months for stable libraries)  
* Security patches applied promptly  
* Clear rationale for each dependency  
* Transitive dependencies understood

**No Code Duplication** (DRY \- Don't Repeat Yourself):

* Similar logic abstracted into shared functions  
* Not just textual duplication \- conceptual duplication matters too  
* But: Don't obsess \- some duplication better than wrong abstraction

#### **Code Smells to Look For**

**Shotgun Surgery** (Martin Fowler):

* Making one conceptual change requires modifying many classes  
* Sign that related behavior is scattered across modules  
* Example: Adding new payment method requires changes in 8 different files

**Feature Envy**:

* Method uses more methods/data from another class than its own  
* Sign the method is in the wrong place

\# Bad: Order logic in Customer class  
class Customer:  
    def calculate\_order\_total(self, order):  
        return sum(order.items) \+ order.tax \+ order.shipping

**Circular Dependencies**:

* Module A imports Module B, which imports Module A  
* Creates initialization problems  
* Makes code hard to understand and test  
* Often indicates poor separation of concerns

**God Object/Class**:

* One class that knows too much or does too much  
* Everything depends on it  
* Hundreds or thousands of lines  
* Impossible to understand or modify safely

**Dependency Version Debt**:

* Libraries 2+ major versions behind current   
* Security vulnerabilities in dependencies  
* Dependencies with known bugs that are fixed in newer versions  
* Mix of very old and very new dependencies (inconsistent maintenance)

**Duplicated Logic**:

\# Bad: Same calculation in multiple places  
def calculate\_price\_with\_tax\_1(price):  
    return price \* 1.08

def calculate\_price\_with\_tax\_2(price):  
    return price \* 1.08

def calculate\_final\_price(price):  
    return price \* 1.08

\# Good: Single source of truth  
TAX\_RATE \= 0.08  
def apply\_tax(price):  
    return price \* (1 \+ TAX\_RATE)

**Hardcoded Configuration**:

* Database URLs in code  
* API keys in source files  
* Environment-specific values scattered throughout  
* Makes deployment to different environments difficult

**Long Parameter Lists**:

* Functions taking 5+ parameters  
* Often indicates missing abstraction  
* Consider: Should these parameters be grouped into an object?

#### **Assessment Questions**

* Trace one feature through the codebase. How many files does it touch?  
* Check dependency versions. When were they last updated?  
* Run `npm audit`, `pip-audit`, or similar. Any security vulnerabilities?  
* Search for duplicated code (similar logic in multiple places)  
* Look for circular imports/dependencies  
* Try to test one component in isolation. Is it possible?  
* Check for hardcoded URLs, paths, or configuration values  
* Look at the largest class/module. What's its line count? What does it do?

---

### **4\. Error Handling & Resilience**

Evaluates how code handles failure cases and unexpected conditions.

| Rating | Summary |
| ----- | ----- |
| ✅ **Established** | Errors handled appropriately, failure modes anticipated, informative error messages, good logging |
| ⚠️ **Emerging** | Basic error handling, some errors silently caught, inconsistent patterns, logging incomplete |
| 🔴 **Needs Attention** | Errors swallowed or ignored, try-catch everything antipattern, null crashes common, poor logging |

#### **What Good Looks Like**

**Explicit Error Handling**:

* Errors are caught at appropriate boundaries  
* Error handling is intentional, not reflexive  
* Errors include context for debugging  
* Different error types handled differently

**Fail Fast Principle** (Dave Farley):

* Validate inputs early  
* Detect problems as close to source as possible  
* Don't propagate invalid state through system  
* Make bugs obvious during development, not production

Example:

def process\_payment(amount, payment\_method):  
    \# Fail fast \- validate immediately  
    if amount \<= 0:  
        raise ValueError(f"Invalid amount: {amount}")  
    if not payment\_method:  
        raise ValueError("Payment method required")  
    if not payment\_method.is\_valid():  
        raise PaymentMethodError("Invalid payment method")  
      
    \# Now proceed with valid inputs  
    return payment\_method.charge(amount)

**Informative Error Messages**:

* Explain what went wrong  
* Provide context (what operation, what values)  
* Suggest how to fix it (when appropriate)  
* Distinguish between user errors and system errors

Bad: `"Error"` Better: `"Invalid email address"` Best: `"Invalid email address 'user@example' - missing domain extension"`

**Defensive Programming**:

* Null/undefined checks where appropriate  
* Input validation at boundaries  
* Assertions for impossible conditions  
* But: Don't catch and ignore errors you should handle

**Structured Logging**:

* Log at appropriate levels (debug, info, warn, error)  
* Include context: operation, user, request ID  
* Structured logs for parsing: JSON format  
* Don't log sensitive data (passwords, tokens, PII)

#### **Code Smells to Look For**

**Swallowed Exceptions**:

\# Bad: Error hidden  
try:  
    risky\_operation()  
except Exception:  
    pass  \# Silently fails\!

\# Also bad: Generic logging without handling  
try:  
    risky\_operation()  
except Exception as e:  
    print(f"Error: {e}")  \# Logged but not handled

\# Better: Handle or propagate with context  
try:  
    risky\_operation()  
except SpecificError as e:  
    logger.error(f"Failed to process order {order\_id}", exc\_info=True)  
    raise OrderProcessingError(f"Could not complete order") from e

**Pokemon Exception Handling** ("Gotta catch 'em all"):

\# Bad: Catches everything including things you shouldn't  
try:  
    do\_something()  
except Exception:  \# Too broad\!  
    \# Now catching KeyboardInterrupt, SystemExit, etc.

**Null/Undefined Dereferencing**:

// Bad: No null check  
function getCustomerEmail(order) {  
    return order.customer.email;  // What if customer is null?  
}

// Better: Explicit null handling  
function getCustomerEmail(order) {  
    if (\!order?.customer?.email) {  
        throw new Error("Order missing customer email");  
    }  
    return order.customer.email;  
}

**No Input Validation**:

* Functions assume inputs are valid  
* No bounds checking on array indices  
* No type checking in dynamic languages  
* Database queries without parameter sanitization

**Generic Error Messages**:

* "An error occurred"  
* "Something went wrong"  
* "Error: 500"  
* No context about what was being attempted

**Insufficient Logging**:

* No logging at all  
* Only logging errors (missing info for debugging)  
* No correlation IDs for tracing requests through system  
* Logging at wrong level (everything as ERROR, or everything as DEBUG)

**Error Code Proliferation**:

* Dozens of specific error codes with no documentation  
* Callers forced to handle every possible error code  
* Alternative: Use exception hierarchy or error types

**Retry Without Backoff**:

* Immediate retry on failure  
* No exponential backoff  
* No circuit breaker pattern for external services  
* Can amplify cascading failures

#### **Assessment Questions**

* Search for empty catch/except blocks  
* Look for generic `except Exception` or `catch (e)` without handling  
* Search for null/undefined access without checks (`.` followed by `.` in JS/TS)  
* Check recent bug reports \- how many are null pointer / undefined errors?  
* Review error messages \- are they helpful?  
* Look at logging \- is there enough context to debug issues?  
* Check input validation \- what happens with malformed data?  
* Look for external API calls \- how are failures handled?  
* Check for sensitive data in logs (passwords, tokens, credit cards)

---

### **5\. Technical Debt Documentation**

Evaluates whether technical debt is visible and tracked.

| Rating | Summary |
| ----- | ----- |
| ✅ **Established** | Known debt documented in issues, TODOs tracked, workarounds explained, regular debt reduction |
| ⚠️ **Emerging** | Some debt tracked, TODOs exist but aren't linked, ad-hoc documentation, debt occasionally addressed |
| 🔴 **Needs Attention** | No debt tracking, scattered TODOs without context, workarounds undocumented, debt accumulates invisibly |

#### **What Good Looks Like**

**Visible Technical Debt**:

* Known debt captured in issue tracker  
* Issues tagged appropriately (e.g., `tech-debt`, `refactor`, `security`)  
* Debt assessed for impact and prioritized  
* Team has shared understanding of major debt areas

**Tracked TODOs**:

\# Good: TODO with tracking  
\# TODO(\#1234): Refactor to use async/await instead of callbacks  
\# This temporary implementation works but is hard to maintain

\# Bad: TODO with no context  
\# TODO: fix this

**Documented Workarounds**:

* Temporary solutions explained in comments  
* Linked to issues describing proper solution  
* Include context about why workaround exists  
* Note any limitations or edge cases

**Architecture Decision Records (ADRs)**:

* Major technical decisions documented  
* Include context, options considered, tradeoffs  
* Explain why current approach was chosen  
* Help future teams understand constraints

Example ADR structure:

\# ADR 001: Use PostgreSQL for Primary Database

\#\# Status: Accepted

\#\# Context  
We need persistent storage for user data and transactions...

\#\# Decision  
We will use PostgreSQL as our primary database.

\#\# Consequences  
Positive: ACID guarantees, rich query capabilities...  
Negative: Requires more operational expertise than...

**Regular Debt Reduction**:

* Scheduled time for paying down debt  
* Debt addressed as part of feature work when opportune  
* Team allocates capacity (e.g., 20% time) to improvement  
* Metrics track debt trends (growing or shrinking)

#### **Code Smells to Look For**

**Invisible Debt**:

* No technical debt in issue tracker  
* Team can list problems but they're not written down  
* New team members surprised by "known issues"  
* Workarounds passed along verbally

**TODO Graveyard**:

\# Patterns that indicate dead TODOs:  
\# TODO: fix before shipping  (but it shipped 2 years ago)  
\# HACK: temporary solution  (from 2018\)  
\# FIXME: this is broken  (no context, no issue number)  
\# XXX: refactor this  (no explanation why)

**Context-Free Comments**:

\# Bad: No explanation  
\# This is weird but necessary  
result \= transform(data)

\# Good: Explains the weirdness  
\# API returns data in reverse order due to legacy sorting bug  
\# See issue \#456 for context. Remove this once API v2 ships.  
result \= transform(reversed(data))

**Accumulating Debt**:

* Same problems reported repeatedly in retros  
* "We'll fix it later" becomes "we'll never fix it"  
* Technical debt growing faster than it's being paid down  
* Development velocity slowing over time

**No Debt Prioritization**:

* All debt treated equally  
* No distinction between:  
  * Blocking current work  
  * Slowing development  
  * Low-impact annoyances  
* Team doesn't know which debt to tackle first

**Undocumented Design Decisions**:

* "Why did we do it this way?" becomes archeology  
* Rationale for architectural choices lost  
* Team repeats past mistakes due to lost context  
* New members can't understand tradeoffs made

**Orphaned Code**:

\# Commented-out code with no explanation  
\# def old\_process\_order(order):  
\#     ...  \# 50 lines of commented code

\# Is this needed? Can it be deleted? Why was it commented out?

#### **Assessment Questions**

* Search codebase for TODO, FIXME, HACK, XXX comments \- how many exist?  
* Pick 5 TODOs \- do they have context? Link to issues? Are they still relevant?  
* Check issue tracker \- is technical debt represented?  
* Ask team: "What are your top 3 technical pain points?" Are they tracked?  
* Look for commented-out code \- why is it there?  
* Check when technical debt was last addressed (not just logged)  
* Look for ADRs or design decision documentation  
* Review workarounds \- are they explained?  
* Check git history of problematic files \- are there patterns of "fixing" same issues?

---

## **Context-Specific Dimensions (assess as relevant)**

### **6\. Developer Experience & Contributor Onboarding**

*Assess for: All open source projects, community-driven development, academic programs*

This dimension evaluates how welcoming and efficient the project is for new and returning contributors. In open source and academic contexts, developer experience directly affects knowledge distribution, contribution breadth, and long-term sustainability. A project with poor DevEx creates barriers to contribution, concentrating knowledge in a few individuals and limiting the community's ability to grow and maintain the codebase.

| Rating | Summary |
| ----- | ----- |
| ✅ **Established** | Fast setup (\<15min), clear contribution path, automated quality checks, good documentation, welcoming to newcomers |
| ⚠️ **Emerging** | Setup works but takes time, some documentation gaps, manual quality enforcement, contribution process could be clearer |
| 🔴 **Needs Attention** | Setup broken or extremely complex, poor/missing docs, no clear contribution path, hostile to new contributors |

#### **What Good Looks Like**

**Fast First Contribution:**

* New contributor can clone and run project in \<15 minutes  
* Can make a small change and see it work in \<30 minutes  
* Can understand where to start contributing in \<1 hour  
* Can submit their first PR with confidence

**Self-Service Setup:**

* Single command setup (or very few commands)  
* Automated dependency installation  
* Database/service initialization handled automatically  
* Environment configuration with sensible defaults  
* Test data seeding automated  
* Works consistently across Mac/Linux/Windows

**Clear Contribution Path:**

* `CONTRIBUTING.md` with step-by-step process  
* Architecture overview (`ARCHITECTURE.md` or in README)  
* Issue labels for newcomers (`good-first-issue`, `help-wanted`, `mentorship-available`)  
* Clear code review expectations and timeline  
* Template for issues and pull requests  
* Examples of good contributions

**Fast Feedback Loops:**

* Unit tests run in seconds  
* Watch mode for development  
* Pre-commit hooks catch issues before commit  
* CI provides clear, actionable feedback  
* Linting/formatting automated  
* Error messages are helpful and specific

**Consistent Development Environment:**

* Dev containers or Docker support available  
* Dependencies version-pinned with lockfiles  
* No reliance on global tool installations  
* Clear documentation of any required tools  
* No undocumented environment assumptions

**Distributed Knowledge:**

* Architecture and design decisions documented  
* Multiple people can review contributions  
* Setup knowledge is written down, not tribal  
* Onboarding doesn't require mentoring  
* Project can continue if key maintainers unavailable

**Welcoming Community Culture:**

* Issues tagged for various skill levels  
* Mentorship offered for complex contributions  
* Timely responses to questions and PRs  
* Code of conduct present and enforced  
* Recognition for contributors

Example of excellent setup experience:

\# Clone repository

git clone https://github.com/org/project

cd project

\# Single-command setup

npm install && npm run dev

\# or

docker-compose up

\# That's it\! Project running at http://localhost:3000

\# Make a change, see it live

\# Run tests: npm test

Example of excellent `CONTRIBUTING.md`:

\# Contributing to \[Project Name\]

\#\# Quick Start

1\. Clone and run: \`npm install && npm run dev\`

2\. Make a change, see it live at http://localhost:3000

3\. Run tests: \`npm test\`

\#\# Your First Contribution

Looking to contribute? Start here:

\- \[Good First Issues\](link) \- Beginner-friendly tasks

\- \[Help Wanted\](link) \- We'd love help with these  

\- \[Architecture Overview\](ARCHITECTURE.md) \- How the code is organized

\- \[Development Guide\](DEVELOPMENT.md) \- Detailed setup and workflow

\#\# Making Changes

1\. Fork and create a branch: \`git checkout \-b feature/my-change\`

2\. Make your changes (hot reload shows them immediately)

3\. Add tests: \`npm test\`

4\. Lint and format: \`npm run lint && npm run format\`

5\. Commit with clear message: \`git commit \-m "feat: add user profile page"\`

6\. Push and open a PR

We typically respond to PRs within 48 hours.

\#\# Development Workflow

\- \`npm run dev\` \- Start dev server with hot reload

\- \`npm test\` \- Run all tests (\~30 seconds)

\- \`npm run test:watch\` \- Run tests in watch mode

\- \`npm run test:unit\` \- Run only unit tests (\~5 seconds)

\- \`npm run lint\` \- Check code style

\- \`npm run format\` \- Auto-fix formatting

\#\# Project Architecture

\- \`/src/components\` \- React components

\- \`/src/api\` \- API layer  

\- \`/src/models\` \- Data models

\- \`/tests\` \- Test files mirror src structure

See \[ARCHITECTURE.md\](ARCHITECTURE.md) for detailed design.

\#\# Getting Help

\- Discord: \[link\] \- Active community chat

\- Office hours: Tuesdays 2-3pm EST

\- Documentation: \[link\]

\- Issues: Tag with \`question\` label

\#\# Code Review Process

1\. Automated checks run (linting, tests, build)

2\. Maintainer reviews within 48 hours

3\. Address feedback  

4\. Maintainer approves and merges

5\. Changes deploy to staging automatically

\#\# Recognition

All contributors are added to our \[Contributors page\](link).

Significant contributions recognized in release notes.

#### **Code Smells to Look For**

**"Works on My Machine" Syndrome:**

\# Bad: Undocumented setup assumptions

\# README says "just run the code"

\# But developer has:

\# \- PostgreSQL 14 installed globally with specific config

\# \- Node 16.x from NVM (not documented which version)

\# \- AWS credentials configured in \~/.aws

\# \- Redis running on non-standard port

\# \- Secret environment variables they set once and forgot about

\# None of this is documented or automated

\# Good: Explicit, automated, self-contained setup

docker-compose up \-d  \# Handles all services with exact versions

npm install           \# Lockfile ensures exact dependency versions  

npm run dev           \# Works immediately with sensible defaults

\# All config in .env.example, all services containerized

**Multi-Hour Setup Gauntlet:**

\# Bad README that loses contributors:

\#\# Setup

1\. Install PostgreSQL 14+ (see PostgreSQL website for your OS)

2\. Create a database named 'myproject\_dev'

3\. Find the migration script somewhere in /scripts

4\. Run migrations (figure out the command)

5\. Install Redis

6\. Install Elasticsearch  

7\. Configure Elasticsearch (see their docs)

8\. Get API keys from Twitter, Stripe, and SendGrid

9\. Set up 15 environment variables (not all documented)

10\. Install specific Python version

11\. Create virtual environment

12\. Install Python dependencies

13\. Install Node dependencies  

14\. Compile assets

15\. Pray

... (20 more steps, each with potential failure points)

\# Good README that welcomes contributors:

\#\# Setup (\< 5 minutes)

1\. Install Docker Desktop

2\. Run: \`docker-compose up \-d\`

3\. Run: \`npm install && npm run dev\`  

4\. Visit: http://localhost:3000

That's it\! The app is running with test data.

\#\# What just happened?

\- Docker started PostgreSQL, Redis, and Elasticsearch

\- npm installed dependencies  

\- Database auto-migrated and seeded with test data

\- Dev server started with hot reload

\#\# Troubleshooting

If Docker fails to start: \[link to common solutions\]

If npm install fails: \[link to dependency issues\]

**No Onboarding Path for Newcomers:**

* No issues labeled `good-first-issue` or `help-wanted`  
* No starter tasks documented or suggested  
* All issues assume deep project knowledge  
* No mentorship, guidance, or support offered  
* Contributing guide assumes contributor expertise  
* No examples of past good contributions  
* First-time contributors left to figure things out alone

Example:

\# Bad: Intimidating and unclear

Issue \#47: Refactor the authentication middleware to use the new 

session management strategy discussed in PR \#234, ensuring backward 

compatibility with the legacy token system while maintaining the 

security constraints from the threat model.

\# Good: Welcoming and specific  

Issue \#47: \[good-first-issue\] Add user-friendly error message

When login fails, we currently show "Error 401". We should show 

"Invalid username or password. Please try again."

What to do:

1\. Find the login handler in src/api/auth.js (line 45\)

2\. Change the error message

3\. Add a test in tests/auth.test.js

4\. See CONTRIBUTING.md for how to run tests

No authentication system experience needed\! We're happy to help 

if you get stuck. This is a great first contribution.

**Slow Feedback Loops:**

\# Bad: Can't test individual components

\# Must start entire application to test anything

\# Start time: 5 minutes

\# Test suite takes 15+ minutes to run

\# No way to run subset of tests

\# Errors don't indicate which test failed

\# Must read through 1000 lines of output to find problem

\# To test one function, developer must:

1\. Start database

2\. Start cache server

3\. Start message queue  

4\. Seed test data

5\. Start application

6\. Wait for initialization

7\. Run all 500 tests

8\. Parse output to find relevant failure

\# Good: Fast, focused testing

pytest tests/test\_api.py::test\_user\_login  \# Runs in \< 1 second

npm run test:watch  \# Continuous feedback on file changes

npm run test:unit   \# Unit tests only, \< 5 seconds

npm run test:integration  \# Integration tests, \< 30 seconds

\# Clear, actionable output:

FAIL tests/test\_api.py::test\_user\_login

Expected: 200

Got: 401

Message: "Invalid password"

\# Test runs in \< 1 second, clear feedback immediately

**Hidden Project Knowledge (Poor Knowledge Distribution):**

* Architecture exists only in maintainer's head  
* Design decisions undocumented  
* "Ask Jane" is the only documentation strategy  
* Tribal knowledge required to contribute effectively  
* No explanation of "why" behind technical choices  
* Key subsystems have single person who understands them  
* Onboarding requires extensive mentoring  
* Can't contribute without direct access to core team

Signs of poor knowledge distribution:

\# Found in code review comments:

"This won't work because of the legacy billing system constraints"

(What constraints? Where documented?)

"We can't change this because of the migration strategy"  

(What migration? What strategy? Where's the plan?)

"This breaks the assumptions in the auth layer"

(What assumptions? Where are they written down?)

\# Better: Written documentation anyone can access

See docs/architecture/billing.md for legacy system constraints

See docs/migrations/2024-q1-auth-upgrade.md for migration plan

See src/auth/README.md for authentication layer design

**Manual Quality Enforcement:**

\# Bad: Endless code review comments about style

PR \#123 review comments:

\- "Please run prettier on this file"

\- "This doesn't match our style guide"  

\- "Add types to these parameters"

\- "Use 2 spaces not 4"

\- "Line too long"

\- "Missing semicolon"

\- "Wrong quote style"

\- "Format this object differently"

... 20+ style nits that tooling should catch automatically

(Wastes reviewer time, discourages contributors)

\# Good: Automated enforcement catches issues pre-commit

\# Developer experience:

git commit \-m "feat: add profile page"

Running pre-commit hooks...

✓ Prettier (formatting)

✓ ESLint (style and bugs)  

✓ TypeScript (type checking)

✓ Tests (unit tests pass)

✓ Build (no build errors)

\[main abc123\] feat: add profile page

\# PR review focuses on design, logic, and architecture

\# Not on style issues that machines can check

**Confusing Contribution Process:**

* No pull request template  
* Unclear branching strategy (should I branch from `main`? `develop`? `staging`?)  
* No CI/CD feedback on PRs  
* Review process opaque (who reviews? how long? what happens next?)  
* Don't know when to expect response  
* Unclear commit message conventions  
* Don't know if change is welcome before spending time  
* No guidance on what makes a good PR

\# Bad: Contributor confusion

"I opened a PR but not sure if I did it right?"

"Should I squash commits?"

"Which branch do I target?"  

"How do I run the checks that CI is running?"

"Is anyone going to review this?"

\# Good: Clear process in CONTRIBUTING.md

\#\# Pull Request Process

1\. Branch from \`main\`: \`git checkout \-b feature/my-feature\`

2\. Make changes and commit: Use conventional commit format

   \- feat: for new features

   \- fix: for bug fixes  

   \- docs: for documentation

3\. Push and open PR against \`main\`

4\. Automated checks will run (takes \~3 minutes)

5\. Maintainer will review within 48 hours (usually faster\!)

6\. Address feedback by pushing new commits

7\. We'll merge once approved (we'll squash for you)

Your PR should include:

\- Tests for new functionality

\- Updated documentation if needed

\- Clear description of what/why

Use our PR template (auto-filled when you open PR)

**Dependency Hell:**

// Bad: No version locking, unpredictable builds

{

  "dependencies": {

    "express": "\*",          // Could be v3 or v5\!

    "react": "^18.0.0",      // Could be 18.0.0 or 18.9.9

    "lodash": "latest"       // Changes every day\!

  }

}

// No lockfile committed

// "It works for me" but breaks for everyone else

// Different developers get different dependency versions

// Good: Locked, reproducible versions  

{

  "dependencies": {

    "express": "4.18.2",     // Exact version

    "react": "18.2.0",       // Exact version

    "lodash": "4.17.21"      // Exact version

  }

}

// Plus package-lock.json committed to git

// Everyone gets identical dependencies

// Reproducible builds

\# Bad: Unpinned Python dependencies

\# requirements.txt

django

requests

pandas

numpy

\# Different developers get different versions

\# Production might differ from development

\# "But it worked on my machine\!"

\# Good: Pinned with lockfile

\# requirements.txt generated from requirements.in

Django==4.2.1

requests==2.31.0  

pandas==2.0.2

numpy==1.24.3

\# Generated via pip-compile

\# Reproducible across all environments

**Hostile or Opaque Atmosphere:**

* No response to issues or PRs (weeks of silence)  
* Dismissive responses to questions  
* No code of conduct  
* Cliquish behavior (inside jokes, unexplained decisions)  
* Contributions rejected without explanation  
* "RTFM" responses to reasonable questions  
* No recognition or thanks for contributions  
* Gatekeeping ("that's not how we do things here")

Signs of poor contributor experience:

\# Issue comments that discourage contribution:

"This has been discussed before, use search"

(No link, no summary, no help)

"This would break things"

(No explanation what or why)

"Won't fix"  

(No justification or discussion)

\# Better responses that encourage contribution:

"Thanks for the issue\! We discussed similar ideas in \#234 

and \#567. The challenge we found was X. If you have ideas 

for addressing that, we'd love to explore this further."

"Good catch\! The challenge is this would affect Y subsystem 

in Z way. See docs/architecture/Y.md for context. If you 

want to tackle this, let's discuss approach first."

"We're not planning to implement this right now because 

\[reason\], but we'd accept a well-tested PR if you're 

interested in working on it. Happy to mentor\!"

**No Developer Tooling Support:**

* No watch mode for live reloading  
* No debug mode or helpful debug output  
* No development tools or browser extensions  
* Must restart entire app to see changes  
* No hot module replacement  
* No source maps for debugging  
* No way to inspect application state

// Bad: No development conveniences

// Must fully restart server (30 seconds) for any change

// No source maps, errors show minified code

// No debug tools or helpful logging

// Painful edit-compile-test cycle

// Good: Modern development experience  

npm run dev

\# Server starts with:

\# \- Hot module replacement (changes appear instantly)

\# \- Source maps (errors show actual source code)

\# \- Debug logging enabled

\# \- React DevTools integration

\# \- API request logging

\# \- Automatic browser refresh

// Make a change → See it immediately

// Error occurs → Clear stack trace with actual code

#### **Assessment Questions**

**Time to First Commit (TTFC):**

* Time a non-developer or new student recruit  
* Ask them to:  
  1. Clone the repository  
  2. Follow setup instructions  
  3. Run the project successfully  
  4. Make a trivial change (fix a typo, change a color)  
  5. Verify their change works  
* How long did this take? (Goal: \<30 minutes total)  
* Where did they get stuck?  
* What was confusing or undocumented?

**Setup Simplicity:**

* Can setup be done with 1-3 commands?  
* Or does it require 10+ manual steps?  
* Does it work on a fresh clone on different OS?  
* Are there undocumented prerequisites?  
* What happens if a step fails? Clear error messages?

**Local Development Cycle:**

* How long to see a code change reflected? (Goal: \<5 seconds)  
* Do tests run automatically on change?  
* How long to run a single unit test? (Goal: \<1 second)  
* How long to run full test suite? (Goal: \<2 minutes)  
* Can you run a subset of tests easily?

**Documentation Quality:**

* Is there a `CONTRIBUTING.md`?  
* Does it explain the contribution process step-by-step?  
* Is there architecture documentation?  
* Are design decisions documented?  
* Is there a development guide?  
* Do docs explain "why" not just "what"?

**Contributor Path Clarity:**

* Are issues labeled for different skill levels?  
* Can a newcomer find appropriate first tasks?  
* Is there a PR template with guidance?  
* Are commit message conventions documented?  
* Is the review process explained?  
* Is expected response time communicated?

**Error Guidance:**

* Introduce a syntax error \- is the error message helpful?  
* Break a test \- does output clearly show what failed?  
* Misconfigure something \- does it fail with clear guidance?  
* Or do errors require deep debugging to understand?

**Knowledge Distribution:**

* If the primary maintainer left, could project continue?  
* Can someone contribute without asking for help?  
* Is setup knowledge documented or tribal?  
* How many people understand each subsystem?  
* Are decisions explained or just implemented?

**Contribution Breadth:**

* How many people have contributed?  
* Do first-time contributors return for second contribution?  
* Are contributions from diverse skill levels?  
* Is there recurring external contribution?  
* Or is it mostly core team?

**Automation Coverage:**

* What quality checks run automatically?  
* What requires manual enforcement?  
* Are formatting, linting, tests automated?  
* Do pre-commit hooks exist?  
* Does CI catch issues before review?

**Community Health:**

* How quickly are issues/PRs responded to? (Goal: \<48 hours)  
* Are responses helpful and welcoming?  
* Is there a code of conduct?  
* Are contributions recognized?  
* Is there mentorship available?

**Tool Support:**

* Can you use standard IDE/editor?  
* Do dev tools work (browser extensions, debuggers)?  
* Is there watch mode for development?  
* Are there helpful npm/make scripts?  
* Do errors have source maps?

#### **Tips for Open Source Projects**

**Optimize for the "Drive-By Contributor":**

* Someone who has 30 minutes to fix a typo or small bug  
* Should be able to clone, fix, test, PR in one sitting  
* Don't force them to understand entire architecture  
* Make small contributions easy and welcoming

**Document Everything:**

* Never assume knowledge is obvious  
* Write for future-you who forgot everything  
* Explain "why" not just "what"  
* Link to relevant background context  
* Keep docs close to code

**Automate Quality:**

* Don't make reviewers enforce style  
* Machines should check what machines can check  
* Pre-commit hooks prevent issues  
* CI should be fast and clear  
* Let reviewers focus on design and logic

**Create Contribution Ladder:**

* `good-first-issue` \- Trivial, well-defined, mentored  
* `help-wanted` \- Valuable, clear scope, some guidance  
* `advanced` \- Complex, requires project knowledge  
* Let people grow from newcomer to maintainer

**Celebrate Contributors:**

* Thank people publicly  
* Credit in release notes  
* Contributors page  
* Recognition builds community

**Reduce Setup Friction:**

* Containerize dependencies  
* Provide sample data  
* Make setup one command  
* Test on clean machine regularly  
* Setup should "just work"

#### **Related Dimensions**

This dimension connects to:

* **Build & Deployment** \- DevEx extends to deployment ease  
* **Technical Debt Documentation** \- Good docs enable good DevEx  
* **Test Coverage & Quality** \- Fast tests enable fast feedback  
* **Observability & Operations** \- Clear logs help contributors debug  
* **Code Complexity & Clarity** \- Readable code is easier to contribute to

---

#### **When DevEx Matters Most**

**Critical importance:**

* Open source projects seeking community contributions  
* Academic programs with rotating student teams  
* Projects with distributed knowledge needs  
* Community-driven development  
* Projects requiring broad contribution base

**Lower priority (but still relevant):**

* Internal closed-source tools with stable team  
* Solo developer projects not seeking contribution  
* Prototypes and experiments  
* Short-lived projects

For academic OSPOs and community-driven projects, **DevEx should be assessed as carefully as any core dimension**—it directly determines whether your project can build a sustainable contributor community.

---

### **7\. Security Practices**

*Assess for: All projects handling sensitive data, user accounts, or public-facing services*

| Rating | Summary |
| ----- | ----- |
| ✅ **Established** | Vulnerabilities tracked and patched, dependencies updated, proper authentication/authorization, sensitive data protected |
| ⚠️ **Emerging** | Some security awareness, irregular updates, inconsistent validation, basic auth with gaps |
| 🔴 **Needs Attention** | Security ignored, dependencies outdated, minimal validation, weak/missing auth, sensitive data exposed |

#### **What Good Looks Like**

**Secure by Default**:

* Authentication required for sensitive operations  
* Authorization checked at boundaries  
* Sensitive data encrypted at rest and in transit  
* Principle of least privilege applied

**Input Validation** (OWASP Top 10 awareness):

* All user input validated and sanitized  
* SQL injection prevented (parameterized queries)  
* XSS prevented (proper escaping/sanitization)  
* Command injection prevented (avoid shell execution with user input)

**Dependency Security**:

* Dependencies scanned for vulnerabilities  
* Security patches applied promptly (within days/weeks)  
* Automated alerts for new vulnerabilities  
* Transitive dependencies understood

**Secrets Management**:

* No secrets in source code  
* Environment variables or secret management service  
* Secrets rotated periodically  
* Access to secrets limited

#### **Code Smells to Look For**

**SQL Injection Vulnerabilities**:

\# Bad: String concatenation  
query \= f"SELECT \* FROM users WHERE username \= '{username}'"

\# Good: Parameterized queries  
query \= "SELECT \* FROM users WHERE username \= ?"  
cursor.execute(query, (username,))

**Secrets in Code**:

\# Bad: Credentials in source  
API\_KEY \= "sk\_live\_abc123xyz789"  
DB\_PASSWORD \= "hunter2"

\# Good: From environment or secret manager  
API\_KEY \= os.environ\['API\_KEY'\]  
DB\_PASSWORD \= secrets\_manager.get\_secret('db\_password')

**No Input Validation**:

* User input passed directly to database  
* File uploads without type/size restrictions  
* No sanitization before rendering in HTML

**Weak Authentication**:

* Passwords stored in plaintext or MD5  
* No rate limiting on login attempts  
* No multi-factor authentication option  
* Session tokens predictable or not invalidated

**Authorization Gaps**:

* No checks before sensitive operations  
* Trusting client-side authorization  
* Horizontal privilege escalation (accessing other users' data)  
* Vertical privilege escalation (regular user doing admin actions)

**Information Disclosure**:

\# Bad: Detailed error to user  
except Exception as e:  
    return f"Database error: {str(e)}"  \# Leaks schema info

\# Good: Generic error to user, detailed log internally  
except Exception as e:  
    logger.error(f"DB error for user {user\_id}", exc\_info=True)  
    return "An error occurred. Please try again."

**Outdated Dependencies**:

* Libraries multiple major versions behind  
* Known CVEs in dependencies  
* No automated vulnerability scanning  
* Last update \>2 years ago

#### **Assessment Questions**

* Run dependency security scanner (npm audit, pip-audit, etc.)  
* Search for hardcoded credentials, API keys, passwords  
* Check authentication implementation \- passwords hashed? Salted?  
* Look for SQL queries \- are they parameterized?  
* Check authorization \- can users access others' data?  
* Review error messages \- do they leak sensitive info?  
* Check HTTPS/TLS usage \- enforced everywhere?  
* Look at file uploads \- validated and restricted?  
* Check session management \- tokens secure and invalidated on logout?  
* Review logging \- any sensitive data (passwords, credit cards, SSNs)?

**Related checkpoint:** This assessment directly informs your Threat Model.

---

### **8\. Performance & Scalability**

*Assess for: Performance-critical projects, high-traffic services, resource-constrained environments*

| Rating | Summary |
| ----- | ----- |
| ✅ **Established** | Performance considered in design, no obvious antipatterns, efficient algorithms, monitoring exists |
| ⚠️ **Emerging** | Some performance awareness, occasional inefficiencies, inconsistent resource cleanup, limited visibility |
| 🔴 **Needs Attention** | Performance not considered, frequent antipatterns, memory leaks, algorithms don't scale, no monitoring |

#### **What Good Looks Like**

**Performance Mindset**:

* Performance requirements understood  
* Algorithm complexity considered  
* Resource usage (CPU, memory, I/O) understood  
* Performance testing in CI/CD

**Efficient Database Access**:

* Queries optimized with proper indices  
* N+1 queries avoided (use joins or eager loading)  
* Pagination for large result sets  
* Connection pooling used

**Resource Management**:

* Connections closed properly  
* File handles released  
* Memory freed when no longer needed  
* Background tasks cleaned up

**Asynchronous Operations**:

* Long-running operations don't block  
* Async/await or promises used appropriately  
* Event-driven architecture where suitable

**Observability**:

* Performance metrics collected  
* Slow operations identified  
* Resource usage monitored  
* Alerts for performance degradation

#### **Code Smells to Look For**

**N+1 Query Problem**:

\# Bad: N+1 queries  
orders \= Order.all()  
for order in orders:  
    print(order.customer.name)  \# Separate query for each order\!

\# Good: Eager loading  
orders \= Order.all().include('customer')  
for order in orders:  
    print(order.customer.name)  \# Customer already loaded

**Inefficient Loops**:

\# Bad: O(n²) when O(n) is possible  
def find\_duplicates(items):  
    duplicates \= \[\]  
    for item in items:  
        if items.count(item) \> 1:  \# Searches entire list each time\!  
            duplicates.append(item)  
    return duplicates

\# Good: O(n) with hash table  
def find\_duplicates(items):  
    counts \= {}  
    for item in items:  
        counts\[item\] \= counts.get(item, 0\) \+ 1  
    return \[item for item, count in counts.items() if count \> 1\]

**Memory Leaks**:

* Event listeners not removed  
* Cached data never evicted  
* Circular references preventing garbage collection  
* Large objects held in memory unnecessarily

**Blocking Operations**:

// Bad: Synchronous I/O blocks event loop  
const data \= fs.readFileSync('large-file.txt');

// Good: Asynchronous  
const data \= await fs.promises.readFile('large-file.txt');

**No Pagination**:

\# Bad: Loading all records  
users \= User.all()  \# Could be millions\!

\# Good: Paginated  
users \= User.page(page\_num).per(50)

**Excessive Database Round-Trips**:

* Multiple queries when one would suffice  
* Data fetched but not used  
* No database connection pooling

**Large Response Payloads**:

* Returning entire objects when only few fields needed  
* No field selection in GraphQL  
* No compression for large responses

**No Caching**:

* Recomputing same expensive operations  
* Repeated database queries for static data  
* No CDN for static assets

#### **Assessment Questions**

* Check database queries for N+1 patterns  
* Look for nested loops \- could they be optimized?  
* Search for `readFileSync`, `sleep()`, blocking operations  
* Check for pagination on list endpoints  
* Look at memory usage over time \- growing without bounds?  
* Check for resource cleanup (connection.close(), file.close())  
* Review algorithms for complexity (O(n²) or worse?)  
* Check if caching is used for expensive operations  
* Look for monitoring/metrics collection  
* Check recent performance issues \- what were root causes?

---

### **9\. Data Management**

*Assess for: Data-intensive projects, applications with complex data models, data pipelines*

| Rating | Summary |
| ----- | ----- |
| ✅ **Established** | Clear data models, validation at boundaries, migrations managed, integrity enforced, backup strategy exists |
| ⚠️ **Emerging** | Basic structures defined, some validation, migration strategy inconsistent, some integrity issues |
| 🔴 **Needs Attention** | Unclear data models, minimal validation, no migration strategy, integrity not enforced, no backup plan |

#### **What Good Looks Like**

**Clear Data Models**:

* Entities and relationships well-defined  
* Normalization appropriate for use case  
* Schema documented  
* Foreign key relationships clear

**Data Validation**:

* Type checking at boundaries  
* Range validation (min/max, length)  
* Format validation (email, phone, etc.)  
* Business rule validation

**Migration Strategy**:

* Database changes versioned and tracked  
* Migrations reversible (up/down)  
* Clear migration history  
* Safe for zero-downtime deployment

**Data Integrity**:

* Foreign key constraints in database  
* Uniqueness constraints where needed  
* Check constraints for business rules  
* Transactions used appropriately

**Backup and Recovery**:

* Regular automated backups  
* Backup restoration tested  
* Point-in-time recovery possible  
* Retention policy defined

#### **Code Smells to Look For**

**Weak Data Models**:

* Generic column names (field1, field2, data)  
* Unclear relationships between entities  
* No normalization or over-normalization  
* Same data stored in multiple places with inconsistencies

**No Validation**:

\# Bad: No validation  
user.email \= request.get('email')  \# Could be anything\!  
user.save()

\# Good: Validation  
email \= request.get('email')  
if not is\_valid\_email(email):  
    raise ValidationError("Invalid email address")  
user.email \= email  
user.save()

**Stringly-Typed Data**:

* Storing everything as strings  
* No use of appropriate data types  
* Dates as strings, booleans as strings  
* Makes querying and validation harder

**Migration Mess**:

* Migrations not in version control  
* Conflicting migration numbers  
* Migrations that can't be reversed  
* Manual database changes not captured

**No Foreign Keys**:

\-- Bad: No foreign key constraint  
CREATE TABLE orders (  
    customer\_id INT  \-- What if this references non-existent customer?  
);

\-- Good: With foreign key  
CREATE TABLE orders (  
    customer\_id INT REFERENCES customers(id) ON DELETE CASCADE  
);

**Data Duplication Without Justification**:

* Same information in multiple tables  
* Denormalization without performance justification  
* Inconsistent data across copies

**Orphaned Data**:

* Records referencing deleted records  
* Cascade deletes not properly configured  
* No cleanup of old/stale data

**SQL in Application Code**:

\# Bad: Raw SQL scattered throughout code  
cursor.execute("SELECT \* FROM users WHERE active \= 1")

\# Better: Data access layer or ORM  
users \= User.query.filter\_by(active=True)

**No Transaction Management**:

\# Bad: Multi-step operation not transactional  
update\_inventory(product\_id, \-quantity)  
create\_order(product\_id, quantity)  \# What if this fails?

\# Good: Wrapped in transaction  
with transaction():  
    update\_inventory(product\_id, \-quantity)  
    create\_order(product\_id, quantity)

#### **Assessment Questions**

* Review database schema \- are relationships clear?  
* Check for foreign key constraints \- are they defined?  
* Look at data validation \- where does it happen?  
* Check migration files \- are they reversible?  
* Look for orphaned or inconsistent data  
* Check backup strategy \- when was last backup? Last restoration test?  
* Look for raw SQL in application code  
* Check for appropriate use of transactions  
* Review data types \- using appropriate types?  
* Look for denormalized data \- is there justification?

---

### **10\. API Design & Contracts**

*Assess for: Projects exposing APIs, microservices, libraries/frameworks, integrations*

| Rating | Summary |
| ----- | ----- |
| ✅ **Established** | Clear consistent API design, proper versioning, good documentation, backward compatibility considered |
| ⚠️ **Emerging** | Generally consistent, some versioning, basic documentation, occasional breaking changes |
| 🔴 **Needs Attention** | Inconsistent patterns, no versioning, poor documentation, frequent breaking changes, cryptic errors |

#### **What Good Looks Like**

**Consistent Design** (REST, GraphQL, or library conventions):

* Predictable patterns across endpoints/methods  
* Clear naming conventions  
* Consistent error handling  
* Standard HTTP status codes (for REST)

**Proper Versioning**:

* Version included in URL (`/api/v1/`) or headers  
* Clear deprecation policy  
* Multiple versions supported during transition  
* Breaking changes increment major version

**Good Documentation**:

* Every endpoint/method documented  
* Request/response examples provided  
* Error codes explained  
* Authentication requirements clear

**Backward Compatibility**:

* Additive changes (new fields) safe  
* Breaking changes avoided or versioned  
* Deprecation warnings before removal  
* Migration guides for breaking changes

**Meaningful Error Responses**:

// Good error response  
{  
    "error": {  
        "code": "INVALID\_EMAIL",  
        "message": "The email address 'user@' is not valid",  
        "field": "email",  
        "docs\_url": "https://api.example.com/docs/errors\#invalid-email"  
    }  
}

#### **Code Smells to Look For**

**Inconsistent Endpoints** (REST example):

\# Bad: No consistent pattern  
POST /createUser  
GET /user-list  
POST /delete\_customer  
GET /getOrderById/123

\# Good: Consistent resource-based  
POST /users  
GET /users  
DELETE /customers/:id  
GET /orders/:id

**No Versioning**:

* Breaking changes deployed directly  
* No way for clients to opt into old behavior  
* Version in code but not exposed to clients

**Poor Error Messages**:

// Bad  
{"error": "500"}  
{"error": "bad request"}  
{"message": "error"}

// Good  
{  
    "error": "VALIDATION\_FAILED",  
    "message": "Order quantity must be positive",  
    "field": "quantity",  
    "value": \-5  
}

**Inconsistent Data Formats**:

// Bad: Inconsistent date formats  
{"created": "2024-01-15", "updated": "01/16/2024"}

// Good: ISO 8601 everywhere  
{"created": "2024-01-15T10:30:00Z", "updated": "2024-01-16T14:22:00Z"}

**Leaky Abstractions**:

* Database column names in API responses  
* Internal IDs exposed unnecessarily  
* Implementation details visible to clients

**Chatty APIs**:

* Multiple requests needed for common operations  
* No batching or bulk endpoints  
* Forces clients to make N+1 requests

**Breaking Changes Without Warning**:

* Fields removed without deprecation period  
* Response structure changed  
* Required fields added to requests  
* No changelog

**No Rate Limiting**:

* Clients can overwhelm API  
* No throttling or quotas  
* No headers indicating limits

**Undocumented Behavior**:

* Side effects not documented  
* Idempotency not specified  
* Caching behavior unclear  
* Authentication requirements unclear

#### **Assessment Questions**

* Review API endpoints/methods \- are patterns consistent?  
* Check for versioning \- how are breaking changes handled?  
* Look at API documentation \- is it comprehensive and current?  
* Test error responses \- are they helpful?  
* Check recent changes \- any breaking changes?  
* Look for deprecation warnings and migration guides  
* Review response formats \- consistent data types and structures?  
* Check rate limiting and throttling  
* Look for bulk/batch operations where appropriate  
* Test with invalid inputs \- are errors clear and helpful?

---

### **11\. Domain Modeling & Business Logic**

*Assess for: Projects with complex business rules, domain-rich applications, systems modeling real-world processes*

| Rating | Summary |
| ----- | ----- |
| ✅ **Established** | Clear domain model, ubiquitous language used, business logic encapsulated, domain concepts explicit |
| ⚠️ **Emerging** | Some domain modeling, inconsistent terminology, business logic somewhat organized, some implicit concepts |
| 🔴 **Needs Attention** | Anemic domain model, terminology mismatch, business logic scattered, domain concepts hidden in infrastructure |

#### **What Good Looks Like**

**Ubiquitous Language** (Eric Evans, "Domain-Driven Design"):

* Code uses same terms as domain experts  
* Classes, methods, variables match business vocabulary  
* Developers and stakeholders speak same language  
* No translation layer between business and code

Example (Course Registration System):

\# Good: Domain language explicit  
class CourseSection:  
    def enroll\_student(self, student: Student) \-\> Enrollment:  
        if self.is\_full():  
            raise SectionFullError(f"Section {self.code} has reached capacity")  
        if not student.has\_prerequisites\_for(self.course):  
            raise PrerequisiteNotMetError()  
        return Enrollment.create(student, self, semester=self.semester)

\# Bad: Generic, doesn't reflect domain  
class Record:  
    def add\_item(self, item):  
        if self.count \>= self.max:  
            raise Exception("Full")  
        self.items.append(item)

**Rich Domain Model**:

* Business logic lives in domain objects  
* Domain objects have behavior, not just data  
* Invariants enforced by the model  
* Domain rules explicit in code

**Explicit Domain Concepts**:

* Important domain concepts are first-class objects  
* Not just primitive types (strings, numbers)  
* Value objects for domain-specific types  
* Entities with clear identity

Example:

\# Bad: Primitives obscure domain concepts  
def calculate\_price(amount: float, discount\_percent: float,   
                   tax\_rate: float) \-\> float:  
    discounted \= amount \* (1 \- discount\_percent / 100\)  
    return discounted \* (1 \+ tax\_rate)

\# Good: Domain concepts explicit  
class Money:  
    def \_\_init\_\_(self, amount: Decimal, currency: str):  
        self.amount \= amount  
        self.currency \= currency  
      
    def apply\_discount(self, discount: Discount) \-\> 'Money':  
        return Money(self.amount \* discount.multiplier, self.currency)

class Discount:  
    def \_\_init\_\_(self, percentage: int):  
        if not 0 \<= percentage \<= 100:  
            raise ValueError("Discount must be 0-100%")  
        self.percentage \= percentage  
        self.multiplier \= Decimal(100 \- percentage) / 100

def calculate\_price(base\_price: Money, discount: Discount,   
                   tax: TaxRate) \-\> Money:  
    discounted \= base\_price.apply\_discount(discount)  
    return tax.apply\_to(discounted)

**Bounded Contexts** (for larger systems):

* Clear boundaries between different parts of domain  
* Same term may mean different things in different contexts  
* Models optimized for their context  
* Integration at boundaries explicit

**Encapsulated Business Logic**:

* Rules enforced by domain layer  
* Can't create invalid domain state  
* Business rules not leaked into controllers/UI  
* Domain layer testable without infrastructure

#### **Code Smells to Look For**

**Anemic Domain Model** (Martin Fowler):

* Domain objects are just data containers  
* All behavior in services  
* Getters and setters everywhere  
* No business logic in domain layer

\# Bad: Anemic \- just data  
class Order:  
    def \_\_init\_\_(self):  
        self.items \= \[\]  
        self.status \= "pending"  
        self.total \= 0.0

\# Service does all the work  
class OrderService:  
    def add\_item(self, order, item, quantity):  
        order.items.append({'item': item, 'qty': quantity})  
        order.total \+= item.price \* quantity  
      
    def can\_checkout(self, order):  
        return order.total \> 0 and order.status \== "pending"  
      
    def checkout(self, order):  
        if not self.can\_checkout(order):  
            raise Exception("Cannot checkout")  
        order.status \= "completed"

\# Good: Rich domain model  
class Order:  
    def \_\_init\_\_(self):  
        self.\_items \= \[\]  
        self.\_status \= OrderStatus.PENDING  
      
    def add\_item(self, item: Item, quantity: int) \-\> None:  
        if self.\_status \!= OrderStatus.PENDING:  
            raise InvalidOperationError("Cannot modify completed order")  
        if quantity \<= 0:  
            raise ValueError("Quantity must be positive")  
        self.\_items.append(LineItem(item, quantity))  
      
    def checkout(self) \-\> None:  
        if not self.can\_checkout():  
            raise CheckoutError("Order cannot be checked out")  
        self.\_status \= OrderStatus.COMPLETED  
      
    def can\_checkout(self) \-\> bool:  
        return (self.\_status \== OrderStatus.PENDING and   
                len(self.\_items) \> 0\)  
      
    def total(self) \-\> Money:  
        return sum(item.subtotal() for item in self.\_items)

**Primitive Obsession**:

* Using strings/numbers for domain concepts  
* No type safety for domain values  
* Easy to mix up parameters  
* Domain rules not enforced

\# Bad: Primitives everywhere  
def create\_course(code: str, credits: int,   
                 instructor\_id: str) \-\> dict:  
    return {  
        'code': code,  
        'credits': credits,  
        'instructor': instructor\_id  
    }

\# Can easily mess up:  
create\_course("CS101", "smith123", 3\)  \# Oops\! Swapped params

\# Good: Value objects and entities  
class CourseCode:  
    def \_\_init\_\_(self, value: str):  
        if not re.match(r'^\[A-Z\]{2,4}\\d{3}$', value):  
            raise ValueError(f"Invalid course code: {value}")  
        self.value \= value

class Credits:  
    def \_\_init\_\_(self, value: int):  
        if not 1 \<= value \<= 6:  
            raise ValueError("Credits must be 1-6")  
        self.value \= value

class InstructorId:  
    def \_\_init\_\_(self, value: str):  
        self.value \= value

def create\_course(code: CourseCode, credits: Credits,   
                 instructor: InstructorId) \-\> Course:  
    return Course(code, credits, instructor)

\# Now type-safe:  
create\_course(  
    CourseCode("CS101"),  
    Credits(3),  
    InstructorId("smith123")  
)

**Terminology Mismatch**:

* Code uses different words than domain experts  
* "Translation" needed between business and tech  
* Business concepts not recognizable in code

\# Bad: Generic tech terminology  
class RecordProcessor:  
    def process\_items(self, records):  
        for record in records:  
            self.validate\_record(record)  
            self.save\_to\_database(record)

\# Ask a biologist: "What's a record processor?"  
\# They won't know what you're talking about

\# Good: Domain language  
class SpecimenCatalog:  
    def accession\_specimens(self, specimens: List\[Specimen\]):  
        """  
        Accession specimens into the collection.  
        'Accession' is the term biologists use for formally  
        adding specimens to a collection.  
        """  
        for specimen in specimens:  
            self.\_verify\_collection\_standards(specimen)  
            self.\_assign\_catalog\_number(specimen)  
            self.\_record\_in\_ledger(specimen)

**Business Logic in Wrong Layer**:

* Controllers/views contain business rules  
* Infrastructure code makes business decisions  
* Domain logic scattered across application

\# Bad: Business logic in controller  
@app.route('/orders/\<id\>/ship', methods=\['POST'\])  
def ship\_order(id):  
    order \= Order.query.get(id)  
    \# Business rules in controller\!  
    if order.status \!= 'paid':  
        return error("Order not paid")  
    if not order.items:  
        return error("Order is empty")  
    if order.shipping\_address is None:  
        return error("No shipping address")  
      
    order.status \= 'shipped'  
    order.shipped\_date \= datetime.now()  
    db.session.commit()  
    send\_email(order.customer.email, "Order shipped")  
    return success()

\# Good: Business logic in domain  
@app.route('/orders/\<id\>/ship', methods=\['POST'\])  
def ship\_order(id):  
    order \= order\_repository.get(id)  
    try:  
        order.ship()  \# Domain logic handles rules  
        order\_repository.save(order)  
        event\_bus.publish(OrderShippedEvent(order))  
        return success()  
    except CannotShipError as e:  
        return error(str(e))

\# Domain model enforces rules  
class Order:  
    def ship(self) \-\> None:  
        if not self.can\_ship():  
            raise CannotShipError(self.\_shipping\_issues())  
        self.\_status \= OrderStatus.SHIPPED  
        self.\_shipped\_date \= datetime.now()  
      
    def can\_ship(self) \-\> bool:  
        return (self.\_status \== OrderStatus.PAID and  
                len(self.\_items) \> 0 and  
                self.\_shipping\_address is not None)

**Implicit Domain Concepts**:

* Important business concepts hidden in code logic  
* No explicit classes for key ideas  
* Domain rules buried in conditionals

\# Bad: "Academic standing" concept implicit  
if student.gpa \>= 3.5:  
    student.eligible\_for\_honors \= True  
elif student.gpa \>= 2.0:  
    student.eligible\_for\_honors \= False  
else:  
    student.status \= "probation"

\# Good: Explicit domain concept  
class AcademicStanding(Enum):  
    HONORS \= "honors"  
    GOOD\_STANDING \= "good\_standing"   
    PROBATION \= "probation"  
    SUSPENSION \= "suspension"

class Student:  
    def academic\_standing(self) \-\> AcademicStanding:  
        """  
        Determine academic standing based on university policy.  
        See Student Handbook section 4.2.  
        """  
        if self.gpa \>= 3.5:  
            return AcademicStanding.HONORS  
        elif self.gpa \>= 2.0:  
            return AcademicStanding.GOOD\_STANDING  
        elif self.gpa \>= 1.5:  
            return AcademicStanding.PROBATION  
        else:  
            return AcademicStanding.SUSPENSION  
      
    def is\_eligible\_for\_honors(self) \-\> bool:  
        return self.academic\_standing() \== AcademicStanding.HONORS

**Leaking Domain Abstractions**:

* Domain model exposed directly to UI  
* Database concerns mixed with domain  
* Domain objects know about infrastructure

\# Bad: Domain coupled to infrastructure  
class Order(db.Model):  \# Database concerns leak into domain  
    \_\_tablename\_\_ \= 'orders'  
    id \= db.Column(db.Integer, primary\_key=True)  
      
    def to\_json(self):  \# Presentation logic in domain  
        return {'id': self.id, 'total': self.total}  
      
    def send\_confirmation\_email(self):  \# Infrastructure in domain  
        smtp\_client.send(...)

\# Good: Domain stays pure  
class Order:  \# Pure domain model  
    def \_\_init\_\_(self, order\_id: OrderId):  
        self.\_id \= order\_id  
        self.\_items \= \[\]  
      
    def total(self) \-\> Money:  
        return sum(item.subtotal() for item in self.\_items)

\# Infrastructure in separate layer  
class OrderRepository:  
    def save(self, order: Order) \-\> None:  
        \# Database logic here  
          
class OrderPresenter:  
    def to\_json(self, order: Order) \-\> dict:  
        \# Presentation logic here

class OrderNotificationService:  
    def send\_confirmation(self, order: Order) \-\> None:  
        \# Email infrastructure here

**God Classes in Domain**:

* One domain class knows/does too much  
* Massive domain objects with many responsibilities  
* Actually multiple bounded contexts mixed together

\# Bad: Customer does everything  
class Customer:  
    \# Identity  
    def \_\_init\_\_(self, id, name, email): ...  
      
    \# Billing (should be separate context)  
    def process\_payment(self, amount): ...  
    def get\_invoices(self): ...  
      
    \# Shipping (should be separate context)  
    def validate\_shipping\_address(self): ...  
    def calculate\_shipping\_cost(self): ...  
      
    \# Marketing (should be separate context)    
    def send\_promotional\_email(self): ...  
    def calculate\_lifetime\_value(self): ...  
      
    \# Support (should be separate context)  
    def log\_support\_ticket(self): ...  
    \# 50 more methods...

\# Good: Bounded contexts separated  
\# Customer context (identity)  
class Customer:  
    def \_\_init\_\_(self, customer\_id, profile): ...

\# Billing context  
class BillingAccount:  
    def \_\_init\_\_(self, customer\_id): ...  
    def process\_payment(self, amount): ...

\# Shipping context    
class ShippingProfile:  
    def \_\_init\_\_(self, customer\_id): ...  
    def validate\_address(self): ...

\# Marketing context  
class MarketingContact:  
    def \_\_init\_\_(self, customer\_id): ...  
    def send\_promotion(self): ...

**Missing Invariants**:

* Invalid domain states possible  
* No enforcement of business rules  
* Domain objects can be corrupted

\# Bad: No invariants enforced  
class BankAccount:  
    def \_\_init\_\_(self):  
        self.balance \= 0  
      
    def withdraw(self, amount):  
        self.balance \-= amount  \# Can go negative\!

\# Good: Invariants enforced  
class BankAccount:  
    def \_\_init\_\_(self, account\_id: AccountId,   
                 account\_type: AccountType):  
        self.\_id \= account\_id  
        self.\_type \= account\_type  
        self.\_balance \= Money.zero()  
        self.\_minimum\_balance \= account\_type.minimum\_balance()  
      
    def withdraw(self, amount: Money) \-\> None:  
        if amount \<= Money.zero():  
            raise ValueError("Withdrawal amount must be positive")  
          
        new\_balance \= self.\_balance \- amount  
        if new\_balance \< self.\_minimum\_balance:  
            raise InsufficientFundsError(  
                f"Withdrawal would violate minimum balance of "  
                f"{self.\_minimum\_balance}"  
            )  
          
        self.\_balance \= new\_balance  
      
    def balance(self) \-\> Money:  
        return self.\_balance  \# No setter \- balance only changes  
                               \# through deposits and withdrawals

#### **Assessment Questions**

**Ubiquitous Language:**

* Read class and method names. Do they match terminology domain experts use?  
* Show code to a domain expert (client, stakeholder). Do they recognize concepts?  
* Is there a glossary of domain terms? Does code match it?  
* Do developers use domain vocabulary in discussions?

**Domain Model Richness:**

* Look at domain classes. Do they have behavior or just getters/setters?  
* Where do business rules live? In domain objects or in services?  
* Can domain objects protect their invariants?  
* Try to create invalid domain state. Is it prevented?

**Primitive Obsession:**

* Count how many string/int parameters represent domain concepts  
* Look for Value Objects. Are domain-specific types defined?  
* Check method signatures. Can you swap parameters by accident?  
* Are there comments explaining what a string/number represents?

**Business Logic Location:**

* Search controllers/views for `if` statements about business rules  
* Look for business logic in database stored procedures  
* Check infrastructure layer for business decisions  
* Can you test business rules without touching infrastructure?

**Explicit Concepts:**

* Are key domain concepts first-class objects?  
* Or are they buried in conditional logic?  
* Do important business ideas have names in the code?  
* Would a domain expert recognize the model?

**Bounded Contexts (if applicable):**

* Does the same term mean different things in different parts of system?  
* Are there clear boundaries between different domain areas?  
* Do models at boundaries translate/adapt?  
* Or is there one giant model trying to serve all contexts?

**Learn from Domain Experts:**

* Schedule 30 minutes with client/stakeholder  
* Walk through code together  
* Ask: "Does this match how you think about the problem?"  
* Note where terminology diverges

#### **Tips for Domain-Rich Projects**

**Start with Language:**

* Document key domain terms  
* Use exact terms from domain experts  
* Rename classes/methods to match  
* Build shared vocabulary

**Make Concepts Explicit:**

* If you're explaining "this string represents X" → make X a type  
* If there are rules about valid values → enforce them in the type  
* If there's complex logic → it probably deserves its own concept

**Protect Invariants:**

* Don't expose setters for domain objects  
* Make changes through methods that enforce rules  
* Construct objects in valid state only  
* Make invalid states unrepresentable

**Keep Domain Pure:**

* Domain layer shouldn't know about database, HTTP, UI  
* Use interfaces for infrastructure dependencies  
* Can test domain logic without any infrastructure

**Evolve with Understanding:**

* Domain models evolve as you learn more  
* Refactor as understanding deepens  
* Work with domain experts continuously  
* Code is a reflection of shared understanding

#### **When DDD Matters Most**

**High value:**

* Complex business rules  
* Domain expert knowledge critical  
* Multiple stakeholders with different views  
* Long-lived systems that will evolve

**Lower value:**

* Simple CRUD applications  
* Thin domain layer (mostly data storage/retrieval)  
* Well-understood technical problems (compilers, graphics, etc.)  
* Short-lived prototypes or experiments

Many student projects start in "lower value" category but may evolve. Consider DDD principles when:

* Client uses lots of domain-specific terminology  
* Business rules are complex or numerous  
* Multiple ways to view the same data/concepts  
* Likely to be maintained long-term by others

---

### **12\. Build & Deployment**

*Assess for: All projects, especially those with complex build processes or multiple environments, and for any research software to enable reproducible science.*

| Rating | Summary |
| ----- | ----- |
| ✅ **Established** | Automated reliable build, clear configuration, repeatable deployment, rollback possible |
| ⚠️ **Emerging** | Build mostly automated, some complexity, environment configs somewhat managed, deployment works but manual |
| 🔴 **Needs Attention** | Manual/brittle build, opaque configuration, hardcoded values, ad-hoc deployment, no rollback |

#### **What Good Looks Like**

**Automated Build** (Dave Farley, "Continuous Delivery"):

* One command to build from clean checkout  
* Build is fast (minutes, not hours)  
* Dependencies automatically fetched  
* Build output is deterministic

**Configuration Management**:

* Environment-specific config separate from code  
* Clear distinction: build-time vs. runtime config  
* Secrets in secret manager, not source control  
* Default values documented

**Repeatable Deployment**:

* Deployment is automated  
* Same process for all environments  
* Deployment can be triggered confidently  
* Artifacts built once, deployed everywhere

**Observable Deployments**:

* Clear success/failure indication  
* Deployment history tracked  
* Health checks after deployment  
* Monitoring alerts on deployment issues

**Rollback Capability**:

* Previous version can be restored quickly  
* Database migrations reversible  
* Feature flags for risky changes

#### **Code Smells to Look For**

**"Works on My Machine"**:

* Build requires specific tools or versions not documented  
* Local environment variables not in docs  
* Build steps executed in specific order not automated  
* Dependencies not specified or pinned

**Configuration in Code**:

\# Bad: Environment-specific values in code  
DB\_HOST \= "prod-db.example.com"  
API\_KEY \= "sk\_live\_abc123"

\# Good: From environment  
DB\_HOST \= os.environ.get('DB\_HOST')  
API\_KEY \= os.environ.get('API\_KEY')

**Manual Deployment Steps**:

* README lists 15 commands to deploy  
* SSH into server and run scripts  
* Manual file copying  
* Different process for each environment

**Brittle Build Process**:

* Build breaks when dependencies update  
* Build requires specific tool versions  
* Flaky builds (pass/fail randomly)  
* Long build times (\>30 minutes)

**No Rollback Plan**:

* "Forward-only" deployment mentality  
* No tagged releases or version history  
* Database migrations can't be reversed  
* Can't quickly restore previous version

**Environment-Specific Builds**:

* Different code branches for different environments  
* Build artifacts differ between environments  
* Separate "staging build" vs. "production build"

**Missing Health Checks**:

* Deployment completes but service is broken  
* No automated verification after deployment  
* Don't know if deployment succeeded until users complain

**Deployment Sprawl**:

\# Bad: Undocumented, fragile deployment script  
ssh prod  
cd /var/www/app  
git pull  
npm install  \# What if this fails halfway?  
pm2 restart all  \# What if new code has syntax error?

#### **Assessment Questions**

* Clone repo fresh \- can you build it? What's required?  
* Check for hardcoded URLs, paths, credentials  
* Look at CI/CD configuration \- how complex is it?  
* Check deployment process \- how many manual steps?  
* Look for environment-specific configurations  
* Check when dependencies were last updated  
* Try to understand rollback procedure  
* Look for health checks after deployment  
* Check build time \- how long does full build take?  
* Look at recent deployment issues \- what went wrong?

---

### **13\. Frontend-Specific**

*Assess for: Web applications, mobile apps, desktop GUI applications*

| Rating | Summary |
| ----- | ----- |
| ✅ **Established** | Logical component structure, clear state management, accessibility considered, responsive, optimized |
| ⚠️ **Emerging** | Component organization mostly clear, state management works but could improve, some accessibility, mostly responsive |
| 🔴 **Needs Attention** | Chaotic component structure, confusing state, accessibility ignored, not responsive, performance issues |

#### **What Good Looks Like**

**Component Architecture**:

* Components have single responsibility  
* Proper composition and reuse  
* Presentational vs. container components separated  
* Props interface is clear

**State Management**:

* State location makes sense (local vs. global)  
* Data flow is unidirectional and clear  
* No prop drilling (passing props through many layers)  
* State updates are predictable

**Accessibility** (WCAG 2.1 guidelines):

* Semantic HTML used  
* Keyboard navigation works  
* Screen reader compatible  
* Sufficient color contrast  
* Alt text for images

**Responsive Design**:

* Works on mobile and desktop  
* Touch-friendly on mobile  
* Breakpoints logical  
* Content readable at all sizes

**Performance**:

* Small bundle sizes  
* Code splitting for large apps  
* Images optimized  
* Lazy loading where appropriate

#### **Code Smells to Look For**

**God Components**:

// Bad: 1000+ line component doing everything  
function Dashboard() {  
    // Handles: auth, data fetching, filtering, sorting,  
    // display, forms, validation, navigation...  
    // 1000 lines of code  
}

**Prop Drilling**:

// Bad: Passing props through many layers  
\<App user={user}\>  
  \<Layout user={user}\>  
    \<Sidebar user={user}\>  
      \<UserMenu user={user}\>  {/\* Finally used here \*/}  
      \</UserMenu\>  
    \</Sidebar\>  
  \</Layout\>  
\</App\>

// Better: Use context or global state

**State Chaos**:

* Multiple sources of truth for same data  
* Unclear when/why state updates  
* Race conditions in state updates  
* Unnecessary re-renders

**No Accessibility**:

\<\!-- Bad: Not accessible \--\>  
\<div onclick="handleClick()"\>Click me\</div\>  
\<img src="photo.jpg"\>  
\<div class="button"\>Submit\</div\>

\<\!-- Good: Accessible \--\>  
\<button onclick="handleClick()"\>Click me\</button\>  
\<img src="photo.jpg" alt="Team photo from 2024 retreat"\>  
\<button type="submit"\>Submit\</button\>

**Not Responsive**:

* Fixed pixel widths throughout  
* Horizontal scrolling on mobile  
* Text too small on mobile  
* Buttons too small to tap

**Performance Issues**:

* Massive bundle sizes (10+ MB)  
* No code splitting  
* Unoptimized images  
* Expensive operations in render  
* Unnecessary re-renders

**Inline Styles Everywhere**:

// Bad: Styling mixed with logic, not reusable  
\<div style={{backgroundColor: '\#fff', padding: '20px', ...}}\>

**CSS Specificity Wars**:

/\* Bad: Increasingly specific selectors fighting each other \*/  
.button { color: blue; }  
div .button { color: red; }  
div.container .button { color: green \!important; }

#### **Assessment Questions**

* Review component hierarchy \- are components focused?  
* Check component sizes \- any \>500 lines?  
* Test keyboard navigation \- can you use the app without mouse?  
* Test with screen reader \- is content accessible?  
* Check color contrast \- meets WCAG AA standards?  
* Test on mobile device \- is it usable?  
* Check bundle size \- how large is the JavaScript?  
* Look for state management \- clear pattern or chaotic?  
* Check for prop drilling \- props passed through many layers?  
* Test performance \- any slow interactions or renders?

---

### **14\. Mobile-Specific**

*Assess for: Native mobile apps (iOS, Android), React Native, Flutter, etc.*

| Rating | Summary |
| ----- | ----- |
| ✅ **Established** | Platform conventions followed, efficient resource use, offline capability, proper lifecycle handling |
| ⚠️ **Emerging** | Generally follows platform patterns, some resource issues, limited offline support, lifecycle mostly handled |
| 🔴 **Needs Attention** | Ignores platform conventions, resource leaks, no offline support, lifecycle issues cause crashes |

#### **What Good Looks Like**

**Platform Conventions**:

* Follows iOS Human Interface Guidelines / Material Design  
* Navigation patterns match platform  
* Uses platform-standard components  
* Gestures match user expectations

**Resource Efficiency**:

* Battery usage reasonable  
* Memory usage stays bounded  
* Network usage optimized  
* App size reasonable (\<100MB ideal)

**Offline Capability**:

* Core functionality works offline  
* Data cached appropriately  
* Sync strategy clear  
* Network failure handled gracefully

**Lifecycle Management**:

* Background/foreground transitions handled  
* State preserved across lifecycle events  
* Resources released when backgrounded  
* Push notifications handled properly

#### **Code Smells to Look For**

**Ignoring Platform Patterns**:

* iOS app using Android-style navigation  
* Custom components instead of platform standards  
* Unusual gesture interpretations

**Battery Drain**:

* Continuous GPS/location tracking when not needed  
* Network polling instead of push notifications  
* Background processes that never sleep  
* Animations running when app backgrounded

**Memory Leaks**:

* Image caches growing unbounded  
* Listeners not unregistered  
* Circular references preventing garbage collection  
* Large objects held unnecessarily

**No Offline Support**:

* App unusable without network  
* No loading states for network requests  
* Data not cached  
* Poor network error handling

**Lifecycle Issues**:

// Bad: State not preserved  
class ViewController {  
    var importantData: Data?  // Lost when app backgrounded\!  
}

// Good: State persisted  
class ViewController {  
    func viewWillDisappear() {  
        UserDefaults.standard.set(importantData, forKey: "data")  
    }  
}

**Large App Size**:

* Unoptimized images/assets  
* Unnecessary dependencies included  
* Debug symbols in release build

#### **Assessment Questions**

* Test app transitions (background/foreground) \- state preserved?  
* Check memory usage over time \- does it grow unbounded?  
* Test offline \- what features still work?  
* Check battery usage \- does it drain battery?  
* Review navigation \- follows platform conventions?  
* Check app size \- how large is the download?  
* Look for location/GPS usage \- is it necessary and efficient?  
* Test on slower/older devices \- performance acceptable?  
* Check for memory leaks using profiler  
* Test push notifications \- handled properly?

---

### **15\. Library/Framework-Specific**

*Assess for: Libraries, frameworks, SDKs, APIs meant for other developers*

| Rating | Summary |
| ----- | ----- |
| ✅ **Established** | Clear public API, comprehensive docs, examples provided, versioned properly, backward compatible |
| ⚠️ **Emerging** | Generally clear API, basic docs, some examples, versioning present, occasional breaking changes |
| 🔴 **Needs Attention** | Confusing API, poor docs, no examples, no versioning, frequent breaking changes |

#### **What Good Looks Like**

**Clear Public API**:

* Distinction between public and private/internal  
* Minimal surface area (expose only what's necessary)  
* Consistent naming and patterns  
* Type safety and documentation

**Comprehensive Documentation**:

* Getting started guide  
* API reference with examples  
* Common use cases covered  
* Migration guides for breaking changes

**Examples and Samples**:

* Working code examples for common scenarios  
* Sample projects demonstrating usage  
* Integration examples with popular tools

**Semantic Versioning**:

* Versions follow SemVer (major.minor.patch)  
* Breaking changes increment major version  
* Backward compatible changes increment minor version  
* Clear changelog

**Backward Compatibility**:

* Deprecation warnings before removal  
* Old APIs supported for reasonable period  
* Migration path documented

#### **Code Smells to Look For**

**Leaky Abstractions**:

* Implementation details exposed as public API  
* Users need to understand internals to use library  
* Abstraction doesn't hide complexity, just moves it

**API Inconsistency**:

\# Bad: Inconsistent patterns  
library.doSomething(arg1, arg2)  
library.perform\_action(arg)  
library.DoOtherThing(arg1, arg2, arg3)

\# Good: Consistent  
library.do\_something(arg1, arg2)  
library.perform\_action(arg)  
library.do\_other\_thing(arg1, arg2, arg3)

**Surprise Behavior**:

* Methods with unexpected side effects  
* Functions that mutate arguments  
* Operations that aren't idempotent when they should be

**Poor Error Messages**:

\# Bad: Unhelpful to library users  
raise Exception("Invalid input")

\# Good: Helpful with context  
raise ValueError(  
    f"Expected positive integer for 'count', got {count}. "  
    f"See docs at https://example.com/docs/api\#count"  
)

**Breaking Changes Without Warning**:

* Methods removed without deprecation  
* Required parameters added  
* Return types changed  
* No migration guide

**Insufficient Documentation**:

\# Bad: No docs  
def process(data, config):  
    ...

\# Good: Comprehensive docs  
def process(data: Dict\[str, Any\], config: Optional\[Config\] \= None) \-\> Result:  
    """  
    Process data according to configuration.  
      
    Args:  
        data: Dictionary containing input data. Must include 'id' and 'value' keys.  
        config: Optional configuration. Defaults to Config() if not provided.  
          
    Returns:  
        Result object with processed data and metadata.  
          
    Raises:  
        ValueError: If data is missing required keys.  
        ProcessingError: If data cannot be processed.  
          
    Example:  
        \>\>\> result \= process({'id': '123', 'value': 42})  
        \>\>\> print(result.status)  
        'success'  
    """

**No Examples**:

* API reference exists but no usage examples  
* Users left to figure out how to use library  
* Common patterns not demonstrated

**Version Chaos**:

* No version numbers  
* Breaking changes in patch versions  
* No changelog

**Large Dependencies**:

* Library brings in many transitive dependencies  
* Conflicts with other libraries users might have  
* Large download size for simple functionality

#### **Assessment Questions**

* Review public API \- is it clear what users should use?  
* Check documentation \- comprehensive and current?  
* Look for examples \- common use cases covered?  
* Check versioning \- follows SemVer?  
* Look at changelog \- breaking changes clearly marked?  
* Test simple use case \- is it easy to get started?  
* Check error messages \- helpful to library users?  
* Look at dependencies \- necessary and reasonable?  
* Review deprecation warnings \- adequate notice given?  
* Check for internal/private API leakage

---

### **16\. Hardware-Dependent Projects**

*Assess for: Embedded systems, IoT, robotics, drone systems, VR/AR, custom hardware interfaces*

| Rating | Summary |
| ----- | ----- |
| ✅ **Established** | Hardware abstraction clear, error handling for hardware failures, resource constraints respected, testing strategy exists |
| ⚠️ **Emerging** | Some hardware abstraction, basic error handling, resource usage mostly managed, limited testing |
| 🔴 **Needs Attention** | Tight coupling to hardware, poor hardware error handling, resource constraints ignored, no testing without hardware |

#### **What Good Looks Like**

**Hardware Abstraction Layer**:

* Hardware-specific code isolated  
* Interfaces defined for hardware interactions  
* Possible to test without physical hardware  
* Multiple hardware implementations supported

Example:

\# Good: Hardware abstracted behind interface  
class MotorController(ABC):  
    @abstractmethod  
    def set\_speed(self, speed: float): ...  
      
class DroneMotorController(MotorController):  \# Real hardware  
    def set\_speed(self, speed: float):  
        \# Talk to actual drone motors  
          
class SimulatedMotorController(MotorController):  \# For testing  
    def set\_speed(self, speed: float):  
        \# Simulate motor behavior

**Graceful Hardware Failure Handling**:

* Sensors can disconnect/fail  
* Detection of hardware failures  
* Fallback behaviors defined  
* Error reporting doesn't crash system

**Resource Awareness**:

* Memory constraints understood and respected  
* CPU/power budgets considered  
* Real-time constraints met  
* Resource monitoring included

**Testing Strategy**:

* Unit tests without hardware (mocks/simulators)  
* Integration tests with hardware in the loop  
* Simulation environments  
* Hardware-in-the-loop testing when needed

**Timing and Concurrency**:

* Real-time requirements documented  
* Deadlines met consistently  
* Race conditions prevented  
* Thread safety ensured

#### **Code Smells to Look For**

**Hardware Tightly Coupled Throughout**:

// Bad: Direct hardware access everywhere  
void processData() {  
    int value \= digitalRead(PIN\_3);  // Hardware call in business logic  
    if (value \> 100\) {  
        digitalWrite(PIN\_5, HIGH);   // Another hardware call  
    }  
}

// Good: Hardware access through abstraction  
void processData(SensorReader& sensor, Actuator& actuator) {  
    int value \= sensor.read();  
    if (value \> 100\) {  
        actuator.activate();  
    }  
}

**No Hardware Error Handling**:

* Assumes sensors always work  
* No timeout on hardware operations  
* System crashes when hardware unavailable  
* No detection of sensor failures

**Ignoring Resource Constraints**:

// Bad: Unbounded memory on embedded system  
char buffer\[10000\];  // 10KB on a device with 32KB RAM\!  
std::vector\<Reading\> history;  // Grows unbounded

// Good: Fixed-size circular buffer  
\#define BUFFER\_SIZE 100  
Reading history\[BUFFER\_SIZE\];  
int history\_index \= 0;

**Timing Assumptions**:

* Assumes operations complete instantly  
* No watchdog timers  
* Missed deadlines not detected  
* Blocking calls in real-time code

**Untestable Without Hardware**:

* No simulation or mocking  
* Can't run tests on development machine  
* Must deploy to hardware to test anything  
* Slow iteration cycle

**Race Conditions**:

// Bad: No synchronization  
// Thread 1  
sensorValue \= readSensor();

// Thread 2  
processValue(sensorValue);  // May see stale or torn read

// Good: Proper synchronization  
{  
    std::lock\_guard\<std::mutex\> lock(sensorMutex);  
    sensorValue \= readSensor();  
}

**Hardcoded Hardware Addresses/Pins**:

// Bad: Hardware config scattered in code  
\#define MOTOR1\_PIN 7  
\#define MOTOR2\_PIN 8  
// Used directly throughout code

// Good: Configuration file  
struct HardwareConfig {  
    int motor1\_pin;  
    int motor2\_pin;  
    // Loaded from config file or EEPROM  
};

**No Calibration Support**:

* Sensors assumed to be perfect  
* No calibration routine  
* No way to adjust for hardware variations  
* Hardcoded sensor ranges

#### **Assessment Questions**

* Can you run tests without physical hardware?  
* Look for hardware abstraction layer \- does it exist?  
* Check error handling \- what happens if sensor fails?  
* Look for resource constraints \- memory/CPU budgets defined?  
* Check for hardcoded pins/addresses/hardware values  
* Look for timing requirements \- documented and met?  
* Check for thread safety in concurrent code  
* Look for simulation or mocking of hardware  
* Check for calibration routines  
* Review recent hardware-related bugs \- what patterns emerge?

---

### **17\. Observability & Operations**

*Assess for: Production services, infrastructure components, long-running systems*

| Rating | Summary |
| ----- | ----- |
| ✅ **Established** | Comprehensive logging, metrics collected, monitoring/alerting configured, operations runbooks exist |
| ⚠️ **Emerging** | Basic logging, some metrics, limited monitoring, ad-hoc operations knowledge |
| 🔴 **Needs Attention** | Poor logging, no metrics, no monitoring, tribal knowledge for operations |

#### **What Good Looks Like**

**Structured Logging**:

* Consistent log levels (DEBUG, INFO, WARN, ERROR)  
* Structured format (JSON) for parsing  
* Context included (request ID, user ID, operation)  
* No sensitive data logged

**Metrics Collection**:

* Key performance indicators tracked  
* Business metrics (orders, signups) tracked  
* System metrics (CPU, memory, latency) collected  
* Metrics enable data-driven decisions

**Monitoring and Alerting**:

* Dashboards show system health  
* Alerts for critical issues  
* Runbooks linked from alerts  
* Alert fatigue avoided (actionable alerts only)

**Tracing and Debugging**:

* Request tracing through distributed system  
* Correlation IDs connect related operations  
* Debug logs available when needed  
* Easy to find relevant logs

**Operations Documentation**:

* Runbooks for common issues  
* Deployment procedures documented  
* Disaster recovery procedures  
* On-call playbooks

#### **Code Smells to Look For**

**Print Statement Debugging**:

\# Bad: Console logging  
print("here")  
print(f"value: {value}")  
print("done")

\# Good: Structured logging  
logger.info("Processing order", extra={  
    'order\_id': order.id,  
    'customer\_id': order.customer\_id,  
    'value': order.total  
})

**No Log Levels**:

* Everything logged as INFO or ERROR  
* Can't filter logs by severity  
* Debug logs in production  
* No way to increase verbosity for troubleshooting

**Missing Context**:

\# Bad: No context  
logger.error("Payment failed")

\# Good: Rich context  
logger.error("Payment failed", extra={  
    'order\_id': order\_id,  
    'customer\_id': customer\_id,  
    'payment\_method': payment\_method,  
    'amount': amount,  
    'error\_code': error.code,  
    'correlation\_id': correlation\_id  
})

**No Metrics**:

* Can't answer: "Is the system healthy?"  
* Can't track: Response times, error rates, throughput  
* No visibility into business metrics  
* Reactive instead of proactive

**Logging Sensitive Data**:

\# Bad: Logging PII and secrets  
logger.info(f"User {username} logged in with password {password}")  
logger.debug(f"API key: {api\_key}")

\# Good: No sensitive data  
logger.info(f"User {username} logged in successfully")  
logger.debug(f"API key present: {api\_key is not None}")

**Alert Fatigue**:

* Dozens of alerts firing constantly  
* Team ignores most alerts  
* Alerts for non-actionable issues  
* No runbooks for responding

**No Request Tracing**:

* Can't follow request through system  
* No correlation ID  
* Impossible to debug distributed operations  
* Logs from different services can't be connected

**Tribal Knowledge**:

* "Ask Sarah how to restart the service"  
* No documentation of common issues  
* Operations knowledge in people's heads  
* New team members struggle to help on-call

**No Health Checks**:

* System could be broken but appears running  
* No way to verify system is healthy  
* Load balancer can't detect unhealthy instances

#### **Assessment Questions**

* Check logs \- are they structured? JSON format?  
* Look for correlation/request IDs  
* Review log levels \- appropriately used?  
* Check for sensitive data in logs  
* Look for metrics collection \- what's tracked?  
* Check monitoring dashboards \- do they exist?  
* Look for alerting configuration  
* Review runbooks \- do they exist and are they current?  
* Check for health check endpoints  
* Look at recent production issues \- how long to diagnose/fix?

---

## **Assessment Workflow**

### **Quick Assessment (20-30 minutes)**

Focus on understanding the biggest risks.

1. **Core Dimensions 1-3** (Complexity, Testing, Dependencies)  
2. **Document top 3 concerns with specific locations**  
3. **Identify 1-2 quick wins**

**Output:**

* High-level health summary  
* Top priority issues  
* Quick wins

---

### **Standard Assessment (45-60 minutes)**

Balanced view across most important areas.

1. **All Core Dimensions (1-5)**  
2. **Select 2-3 relevant Context-Specific Dimensions**  
3. **Document findings with code locations**  
4. **Prioritize into High/Medium/Low**  
5. **Identify patterns across codebase**

**Output:**

* Findings by dimension  
* Prioritized list of issues  
* Common patterns observed  
* Recommended next steps

---

### **Deep Assessment (90+ minutes)**

Comprehensive evaluation for handoff or major decisions.

1. **Review all relevant dimensions thoroughly**  
2. **Document detailed findings with examples**  
3. **Create prioritized technical debt backlog**  
4. **Identify architectural concerns**  
5. **Recommend refactoring strategies**  
6. **Connect to related activities (Threat Model, Backlog Audit, etc.)**

**Output:**

* Comprehensive technical debt inventory  
* Architectural recommendations  
* Refactoring roadmap  
* Risk assessment  
* Resource estimates for addressing debt

---

## **Recording Your Findings**

For each dimension assessed, document using this structure:

### **HIGH PRIORITY**

*Blocks development or poses significant risk*

**Location:** `src/payment/processor.py:lines 45-67`

**Problem:** Payment processing has no error handling. If payment gateway times out or returns error, exception bubbles up and crashes the order processing system. No retry logic, no fallback.

**Impact:** Users lose orders, system crashes, payment inconsistencies. Happened 3 times last week according to logs.

**Recommendation:** Add timeout handling, retry logic with exponential backoff, log failures with correlation IDs. Wrap in try-catch with graceful degradation.

---

### **MEDIUM PRIORITY**

*Slows development, increases maintenance burden*

**Location:** `src/models/order.py:lines 120-250`

**Problem:** `Order` class has grown to 300+ lines with methods handling validation, persistence, email notifications, PDF generation, and analytics tracking. Single Responsibility Principle violated.

**Impact:** Hard to test, hard to modify without breaking something, hard for new developers to understand, changes require touching many concerns.

**Recommendation:** Extract concerns into separate classes: `OrderValidator`, `OrderRepository`, `OrderNotifier`, `OrderAnalytics`. Keep `Order` as a data model with business logic only.

---

### **QUICK WINS**

*High impact, relatively low effort*

**Location:** `tests/` (multiple files)

**Problem:** Test names don't describe what's being tested. Examples: `test_1()`, `test_order()`, `test_method()`. Makes it impossible to understand what broke when test fails.

**Impact:** When tests fail, unclear what functionality is broken. Slows debugging and reduces confidence in test suite.

**Recommendation:** Rename tests to describe behavior: `test_order_total_includes_tax()`, `test_order_rejects_negative_quantity()`. Takes \~2 hours to rename all tests, huge improvement in clarity.

---

### **PATTERNS OBSERVED**

*Recurring issues across the codebase*

**Pattern:** N+1 query problem appears in 8 different controllers/views.

**Prevalence:** Every list view (users, orders, products, etc.) exhibits this pattern.

**Root Cause:** ORM configured with lazy loading by default, team not aware of performance implications.

**Impact:** Page load times of 5-10 seconds for pages with modest amounts of data.

**Recommendation:** Enable eager loading for common relationships, add performance tests for list views, educate team on N+1 detection. Consider adding automated check in CI.

---

## **Connecting to Other Activities**

This technical debt assessment naturally connects to other program activities:

**Threat Modeling:**

* Security dimension findings highlight attack surfaces  
* Missing input validation becomes threat vectors  
* Outdated dependencies are vulnerabilities  
* Error handling gaps can leak information

**Backlog Audit:**

* Technical debt findings should become tracked issues  
* Prioritization informs backlog ordering  
* Quick wins can be added to next sprint  
* Patterns suggest systemic improvements needed

**System Design Documentation:**

* Architecture and coupling findings inform design artifacts  
* Identified debt shows gaps between intended and actual design  
* Refactoring recommendations shape future architecture

**Product Strategy:**

* Technical constraints affect feature feasibility  
* Debt paydown should be in roadmap  
* Technical debt impacts velocity and timelines  
* Security/performance debt affects user experience

**Handoff Preparation:**

* Assessment documents current state for next team  
* Prioritized debt gives clear starting point  
* Patterns identified help next team avoid same issues  
* Quick wins give new team easy orientation tasks

---

## **Tips for Effective Assessment**

**Be Specific:**

* "Code quality issues" → "Functions exceed 100 lines in 8 files"  
* "Tests need improvement" → "Payment processing has no test coverage"  
* "Performance problems" → "N+1 query in orders list causes 10s load time"

**Focus on Impact:**

* Not just "this is bad" but "this blocks users from X"  
* Connect technical debt to business/user impact  
* Help prioritize by quantifying problems

**Balance Breadth and Depth:**

* Quick scan: Broad but shallow  
* Standard: Balanced  
* Deep dive: Comprehensive  
* Choose based on available time and need

**Look for Patterns:**

* Same issue in multiple places suggests systemic problem  
* Patterns often more important than individual issues  
* Root cause may be lack of knowledge, standards, or tooling

**Stay Constructive:**

* Frame as opportunities for improvement  
* Identify quick wins alongside big problems  
* Acknowledge constraints team faced  
* Goal is actionable insights, not blame

**Use Tools:**

* Static analysis (ESLint, Pylint, etc.)  
* Security scanners (npm audit, OWASP Dependency Check)  
* Code coverage tools  
* Complexity metrics (cyclomatic complexity)  
* But: Don't rely solely on automated tools

**Test Your Findings:**

* Can you point to specific code?  
* Would another developer agree?  
* Is the impact real or theoretical?  
* Is your recommendation feasible?

---

## **Acknowledgements**

These technical debt assessment materials were developed collaboratively with Gemini (Google AI) and Claude (Anthropic AI) to support Tech Leads in the Opening Skills & Solutions program at Saint Louis University. The rubric and guidance document synthesize established software engineering principles with practical assessment approaches tailored for academic open source program offices.

The frameworks draw on best practices from industry leaders including Martin Fowler, Dave Farley, Eric Evans, and others cited throughout, adapted for the specific context of student-led open source development teams.

---

## **References**

This rubric draws on established software engineering principles and practices:

**Books & Articles:**

Farley, David. *Modern Software Engineering: Doing What Works to Build Better Software Faster*. Addison-Wesley Professional, 2021\. 

Feathers, Michael. *Working Effectively with Legacy Code*. Pearson Education, 2004\. 

Fogel, Karl. *Producing Open Source Software: How to Run a Successful Free Software Project*. 2nd ed., O’Reilly Media, 2017\. 

Forsgren, Nicole, Jez Humble, and Gene Kim. *Accelerate: The Science of Lean Software and DevOps: Building and Scaling High Performing Technology Organizations*. IT Revolution Press, 2018\. 

Fowler, Martin. *Refactoring: Improving the Design of Existing Code*. 2nd ed., Addison-Wesley Professional, 2018\. 

Humble, Jez, and David Farley. *Continuous Delivery: Reliable Software Releases through Build, Test, and Deployment Automation*. Addison-Wesley Professional, 2010\. Martin, Robert C. 

*Clean Code: A Handbook of Agile Software Craftsmanship*. Pearson Education, 2008\. 

Skelton, Matthew, and Manuel Pais. *Team Topologies: Organizing Business and Technology Teams for Fast Flow*. IT Revolution Press, 2019\. 

Wilson, Greg, et al. "Good Enough Practices in Scientific Computing." *PLOS Computational Biology*, vol. 13, no. 6, 2017, pp. e1005510. DOI: [10.1371/journal.pcbi.1005510](https://doi.org/10.1371/journal.pcbi.1005510).

**Online Resources:**

* OWASP Top 10: https://owasp.org/www-project-top-ten/  
* Martin Fowler's Refactoring Catalog: https://refactoring.com/catalog/  
* Dave Farley's Continuous Delivery YouTube channel

**Principles Referenced:**

* SOLID principles (Single Responsibility, etc.)  
* DRY (Don't Repeat Yourself)  
* YAGNI (You Aren't Gonna Need It)  
* Fail Fast  
* Separation of Concerns  
* Dependency Inversion  
* Test-Driven Development practices

---

*This rubric is a living document. Adapt dimensions and criteria to your project's specific context.*

