**38.** Design a Document Approval Workflow using the State pattern. States: Draft, Submitted, UnderReview, Approved/Rejected, Published.

[Your answer here]

========== THIS SECTION IS THE ANSWER ==========

## Document Approval Workflow — State Pattern

**Pattern:** State (Behavioral)
**Why State?** Document behavior depends entirely on its approval stage. Each state has different allowed actions and transition rules.

**SOLID Principles Applied:**
- **SRP:** Each state handles its own validation and transition logic
- **OCP:** Add new states (NeedsRevision, Archived) without modifying existing states
- **DIP:** Document context depends on State interface, not concrete states

### Text UML Diagram

```
State Transitions:
  Draft ──submit──→ Submitted
  Submitted ──assignReviewer──→ UnderReview
  UnderReview ──approve──→ Approved
  UnderReview ──reject──→ Rejected
  Rejected ──revise──→ Draft
  Approved ──publish──→ Published
  Published ──(terminal state)──

┌──────────────┐      ┌────────────────────┐
│   Document   │─────→│  <<interface>>      │
│──────────────│      │  ApprovalState      │
│ -state       │      │────────────────────│
│ -title       │      │ +submit()           │
│ -content     │      │ +assignReviewer()   │
│ -history     │      │ +approve()          │
│ +submit()    │      │ +reject(reason)     │
│ +approve()   │      │ +revise()           │
│ +reject()    │      │ +publish()          │
│ +publish()   │      └────────┬───────────┘
└──────────────┘               │ implements
                    ┌──────────┼──────────┐──────────┐
                  Draft  Submitted  UnderReview  Approved
                         Rejected   Published
```

### Key Design Decisions

1. **Audit trail** — Every state transition logged with who, when, and why
2. **Role-based transitions** — Only certain roles can trigger certain transitions (author submits, reviewer approves)
3. **Rejection with reason** — Required for traceability; goes back to Draft for revision
4. **Terminal state** — Published is final; document is immutable

### Java Implementation

```java
import java.util.*;

// ============================================================
// DOMAIN MODELS
// ============================================================

enum Role { AUTHOR, REVIEWER, PUBLISHER }

class User {
    private final String name;
    private final Role role;

    public User(String name, Role role) {
        this.name = name;
        this.role = role;
    }

    public String getName() { return name; }
    public Role getRole() { return role; }
}

class StateTransition {
    private final String fromState;
    private final String toState;
    private final String performedBy;
    private final String comment;
    private final long timestamp;

    public StateTransition(String from, String to, String by, String comment) {
        this.fromState = from;
        this.toState = to;
        this.performedBy = by;
        this.comment = comment;
        this.timestamp = System.currentTimeMillis();
    }

    @Override
    public String toString() {
        return String.format("%s -> %s by %s: %s", fromState, toState, performedBy, comment);
    }
}

// ============================================================
// STATE INTERFACE
// ============================================================

interface ApprovalState {
    void submit(Document doc, User user);
    void assignReviewer(Document doc, User assigner, User reviewer);
    void approve(Document doc, User user, String comment);
    void reject(Document doc, User user, String reason);
    void revise(Document doc, User user);
    void publish(Document doc, User user);
    String getStateName();
}

// ============================================================
// DOCUMENT (CONTEXT)
// ============================================================

class Document {
    private final String id;
    private String title;
    private String content;
    private ApprovalState currentState;
    private User author;
    private User assignedReviewer;
    private final List<StateTransition> history = new ArrayList<>();

    public Document(String id, String title, String content, User author) {
        this.id = id;
        this.title = title;
        this.content = content;
        this.author = author;
        this.currentState = new DraftState();
    }

    // Delegate to state
    public void submit(User user) { currentState.submit(this, user); }
    public void assignReviewer(User assigner, User reviewer) { currentState.assignReviewer(this, assigner, reviewer); }
    public void approve(User user, String comment) { currentState.approve(this, user, comment); }
    public void reject(User user, String reason) { currentState.reject(this, user, reason); }
    public void revise(User user) { currentState.revise(this, user); }
    public void publish(User user) { currentState.publish(this, user); }

    // State management
    public void setState(ApprovalState newState, String by, String comment) {
        history.add(new StateTransition(currentState.getStateName(), newState.getStateName(), by, comment));
        System.out.printf("[%s] %s -> %s (by %s)%n", id, currentState.getStateName(), newState.getStateName(), by);
        this.currentState = newState;
    }

    public String getId() { return id; }
    public String getTitle() { return title; }
    public void setContent(String content) { this.content = content; }
    public User getAuthor() { return author; }
    public User getAssignedReviewer() { return assignedReviewer; }
    public void setAssignedReviewer(User reviewer) { this.assignedReviewer = reviewer; }
    public String getStateName() { return currentState.getStateName(); }
    public List<StateTransition> getHistory() { return Collections.unmodifiableList(history); }
}

// ============================================================
// CONCRETE STATES
// ============================================================

class DraftState implements ApprovalState {
    @Override
    public void submit(Document doc, User user) {
        if (user.getRole() != Role.AUTHOR) {
            System.out.println("Only authors can submit documents");
            return;
        }
        doc.setState(new SubmittedState(), user.getName(), "Submitted for review");
    }

    @Override public void assignReviewer(Document doc, User a, User r) { invalid("assign reviewer"); }
    @Override public void approve(Document doc, User u, String c) { invalid("approve"); }
    @Override public void reject(Document doc, User u, String r) { invalid("reject"); }
    @Override public void revise(Document doc, User u) { System.out.println("Already in draft"); }
    @Override public void publish(Document doc, User u) { invalid("publish"); }
    @Override public String getStateName() { return "DRAFT"; }
    private void invalid(String action) { System.out.println("Cannot " + action + " a draft document"); }
}

class SubmittedState implements ApprovalState {
    @Override
    public void assignReviewer(Document doc, User assigner, User reviewer) {
        if (reviewer.getRole() != Role.REVIEWER) {
            System.out.println("Assigned user must have REVIEWER role");
            return;
        }
        doc.setAssignedReviewer(reviewer);
        doc.setState(new UnderReviewState(), assigner.getName(),
                "Assigned to " + reviewer.getName());
    }

    @Override public void submit(Document doc, User u) { System.out.println("Already submitted"); }
    @Override public void approve(Document doc, User u, String c) { invalid("approve"); }
    @Override public void reject(Document doc, User u, String r) { invalid("reject"); }
    @Override public void revise(Document doc, User u) { invalid("revise"); }
    @Override public void publish(Document doc, User u) { invalid("publish"); }
    @Override public String getStateName() { return "SUBMITTED"; }
    private void invalid(String action) { System.out.println("Cannot " + action + " — needs reviewer assignment first"); }
}

class UnderReviewState implements ApprovalState {
    @Override
    public void approve(Document doc, User user, String comment) {
        if (user.getRole() != Role.REVIEWER) {
            System.out.println("Only reviewers can approve");
            return;
        }
        doc.setState(new ApprovedState(), user.getName(), comment);
    }

    @Override
    public void reject(Document doc, User user, String reason) {
        if (user.getRole() != Role.REVIEWER) {
            System.out.println("Only reviewers can reject");
            return;
        }
        doc.setState(new RejectedState(), user.getName(), "Rejected: " + reason);
    }

    @Override public void submit(Document doc, User u) { System.out.println("Already under review"); }
    @Override public void assignReviewer(Document doc, User a, User r) { System.out.println("Reviewer already assigned"); }
    @Override public void revise(Document doc, User u) { invalid("revise"); }
    @Override public void publish(Document doc, User u) { invalid("publish"); }
    @Override public String getStateName() { return "UNDER_REVIEW"; }
    private void invalid(String action) { System.out.println("Cannot " + action + " during review"); }
}

class ApprovedState implements ApprovalState {
    @Override
    public void publish(Document doc, User user) {
        if (user.getRole() != Role.PUBLISHER) {
            System.out.println("Only publishers can publish");
            return;
        }
        doc.setState(new PublishedState(), user.getName(), "Published");
    }

    @Override public void submit(Document doc, User u) { invalid(); }
    @Override public void assignReviewer(Document doc, User a, User r) { invalid(); }
    @Override public void approve(Document doc, User u, String c) { System.out.println("Already approved"); }
    @Override public void reject(Document doc, User u, String r) { invalid(); }
    @Override public void revise(Document doc, User u) { invalid(); }
    @Override public String getStateName() { return "APPROVED"; }
    private void invalid() { System.out.println("Document is approved — can only be published"); }
}

class RejectedState implements ApprovalState {
    @Override
    public void revise(Document doc, User user) {
        if (user.getRole() != Role.AUTHOR) {
            System.out.println("Only the author can revise");
            return;
        }
        doc.setState(new DraftState(), user.getName(), "Revised after rejection");
    }

    @Override public void submit(Document doc, User u) { System.out.println("Revise first before resubmitting"); }
    @Override public void assignReviewer(Document doc, User a, User r) { invalid(); }
    @Override public void approve(Document doc, User u, String c) { invalid(); }
    @Override public void reject(Document doc, User u, String r) { System.out.println("Already rejected"); }
    @Override public void publish(Document doc, User u) { invalid(); }
    @Override public String getStateName() { return "REJECTED"; }
    private void invalid() { System.out.println("Document is rejected — revise to resubmit"); }
}

class PublishedState implements ApprovalState {
    @Override public void submit(Document doc, User u) { terminal(); }
    @Override public void assignReviewer(Document doc, User a, User r) { terminal(); }
    @Override public void approve(Document doc, User u, String c) { terminal(); }
    @Override public void reject(Document doc, User u, String r) { terminal(); }
    @Override public void revise(Document doc, User u) { terminal(); }
    @Override public void publish(Document doc, User u) { terminal(); }
    @Override public String getStateName() { return "PUBLISHED"; }
    private void terminal() { System.out.println("Document is published and immutable"); }
}

// ============================================================
// CLIENT USAGE
// ============================================================

class DocumentWorkflowDemo {
    public static void main(String[] args) {
        User author = new User("Alice", Role.AUTHOR);
        User reviewer = new User("Bob", Role.REVIEWER);
        User publisher = new User("Charlie", Role.PUBLISHER);

        Document doc = new Document("DOC-001", "API Design Guide", "Content...", author);

        // Happy path: Draft -> Submit -> Review -> Approve -> Publish
        doc.submit(author);
        doc.assignReviewer(publisher, reviewer); // anyone can assign
        doc.approve(reviewer, "Looks good!");
        doc.publish(publisher);

        // Try action on published doc
        doc.submit(author); // "Document is published and immutable"

        // Print audit trail
        System.out.println("\n--- Audit Trail ---");
        doc.getHistory().forEach(System.out::println);

        // Rejection path
        System.out.println("\n=== Rejection Flow ===");
        Document doc2 = new Document("DOC-002", "Security Policy", "Draft...", author);
        doc2.submit(author);
        doc2.assignReviewer(publisher, reviewer);
        doc2.reject(reviewer, "Missing threat model section");
        doc2.revise(author);
        doc2.submit(author); // back to submitted

        System.out.println("\n--- Doc2 History ---");
        doc2.getHistory().forEach(System.out::println);
    }
}
```

### Interview Talking Points

- **State vs Strategy:** Both encapsulate behavior behind interface. Key difference: State transitions happen *internally* (state changes itself); Strategy is set *externally* by client.
- **Real-world:** JIRA workflow, GitHub PR reviews, insurance claim processing — all state machines. Spring StateMachine library provides declarative state machine configuration.
- **Why not enum + switch?** Enum-based works for simple FSMs. But when each state has 6+ methods with different validation logic, the switch becomes unmaintainable. State pattern scales.
