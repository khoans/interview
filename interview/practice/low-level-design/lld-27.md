**27.** Design an Access Control System using the Proxy pattern. Check permissions before allowing document access.

[Your answer here]

========== THIS SECTION IS THE ANSWER ==========

## Access Control System — Proxy Pattern

**Pattern:** Protection Proxy (Structural)
**Why Protection Proxy?** Intercepts access to documents and enforces permission checks without modifying the document service itself.

**SOLID Principles Applied:**
- **SRP:** DocumentService handles storage; Proxy handles authorization
- **OCP:** Add new permission rules without changing document access logic
- **LSP:** Proxy is substitutable wherever DocumentService is expected

### Text UML Diagram

```
┌──────────────────────┐
│    <<interface>>      │
│   DocumentService     │
│──────────────────────│
│ +viewDocument(id)     │
│ +editDocument(id,c)   │
│ +deleteDocument(id)   │
│ +listDocuments()      │
└──────────┬───────────┘
           │ implements
     ┌─────┴──────┐
     │            │
┌────▼───┐  ┌────▼──────────────┐     ┌──────────────┐
│  Real  │  │ AccessControl     │────→│ Permission   │
│Document│  │ Proxy             │     │ Manager      │
│Service │  │───────────────────│     │──────────────│
│        │  │ -realService      │     │ -roles       │
│        │  │ -permissionMgr    │     │ -permissions │
│        │  │ -currentUser      │     │ +hasAccess() │
└────────┘  └───────────────────┘     └──────────────┘

Client ──→ AccessControlProxy ──check──→ PermissionManager
                │                              │
                └──if allowed──→ RealDocumentService
```

### Key Design Decisions

1. **Role-based access control (RBAC)** — Users have roles, roles have permissions per action
2. **Document ownership** — Owner always has full access; others depend on role
3. **Fail-closed** — Deny by default if no explicit permission found
4. **Audit on denial** — Log unauthorized access attempts for security review

### Java Implementation

```java
import java.util.*;

// ============================================================
// DOMAIN MODELS
// ============================================================

enum Role { VIEWER, EDITOR, ADMIN }

enum Action { VIEW, EDIT, DELETE, LIST }

class User {
    private final String id;
    private final String name;
    private final Role role;

    public User(String id, String name, Role role) {
        this.id = id;
        this.name = name;
        this.role = role;
    }

    public String getId() { return id; }
    public String getName() { return name; }
    public Role getRole() { return role; }
}

class Document {
    private final String id;
    private String content;
    private final String ownerId;
    private final boolean confidential;

    public Document(String id, String content, String ownerId, boolean confidential) {
        this.id = id;
        this.content = content;
        this.ownerId = ownerId;
        this.confidential = confidential;
    }

    public String getId() { return id; }
    public String getContent() { return content; }
    public void setContent(String content) { this.content = content; }
    public String getOwnerId() { return ownerId; }
    public boolean isConfidential() { return confidential; }
}

// ============================================================
// INTERFACE — document operations contract
// ============================================================

interface DocumentService {
    Document viewDocument(String documentId);
    void editDocument(String documentId, String newContent);
    void deleteDocument(String documentId);
    List<Document> listDocuments();
}

// ============================================================
// REAL SERVICE — actual document storage operations
// ============================================================

class RealDocumentService implements DocumentService {
    private final Map<String, Document> documents = new HashMap<>();

    public void addDocument(Document doc) {
        documents.put(doc.getId(), doc);
    }

    @Override
    public Document viewDocument(String documentId) {
        Document doc = documents.get(documentId);
        if (doc == null) {
            throw new NoSuchElementException("Document not found: " + documentId);
        }
        System.out.println("Viewing document: " + documentId);
        return doc;
    }

    @Override
    public void editDocument(String documentId, String newContent) {
        Document doc = documents.get(documentId);
        if (doc == null) {
            throw new NoSuchElementException("Document not found: " + documentId);
        }
        doc.setContent(newContent);
        System.out.println("Document edited: " + documentId);
    }

    @Override
    public void deleteDocument(String documentId) {
        if (documents.remove(documentId) == null) {
            throw new NoSuchElementException("Document not found: " + documentId);
        }
        System.out.println("Document deleted: " + documentId);
    }

    @Override
    public List<Document> listDocuments() {
        return new ArrayList<>(documents.values());
    }

    public Document getDocument(String id) {
        return documents.get(id);
    }
}

// ============================================================
// PERMISSION MANAGER — centralized access rules
// ============================================================

class PermissionManager {
    // Role -> Set of allowed actions
    private final Map<Role, Set<Action>> rolePermissions = new EnumMap<>(Role.class);

    public PermissionManager() {
        rolePermissions.put(Role.VIEWER, EnumSet.of(Action.VIEW, Action.LIST));
        rolePermissions.put(Role.EDITOR, EnumSet.of(Action.VIEW, Action.EDIT, Action.LIST));
        rolePermissions.put(Role.ADMIN, EnumSet.allOf(Action.class));
    }

    public boolean hasPermission(User user, Action action, Document document) {
        // Owner always has full access
        if (document != null && document.getOwnerId().equals(user.getId())) {
            return true;
        }

        // Confidential docs: only ADMIN or owner
        if (document != null && document.isConfidential() && user.getRole() != Role.ADMIN) {
            return false;
        }

        // Check role-based permission
        Set<Action> allowed = rolePermissions.get(user.getRole());
        return allowed != null && allowed.contains(action);
    }
}

// ============================================================
// ACCESS DENIED EXCEPTION
// ============================================================

class AccessDeniedException extends RuntimeException {
    private final User user;
    private final Action action;
    private final String resourceId;

    public AccessDeniedException(User user, Action action, String resourceId) {
        super(String.format("User '%s' (role=%s) denied %s on '%s'",
                user.getName(), user.getRole(), action, resourceId));
        this.user = user;
        this.action = action;
        this.resourceId = resourceId;
    }
}

// ============================================================
// PROTECTION PROXY — enforces access control
// ============================================================

class AccessControlProxy implements DocumentService {
    private final RealDocumentService realService;
    private final PermissionManager permissionManager;
    private final User currentUser;
    private final List<String> accessLog = new ArrayList<>();

    public AccessControlProxy(RealDocumentService realService,
                              PermissionManager permissionManager,
                              User currentUser) {
        this.realService = realService;
        this.permissionManager = permissionManager;
        this.currentUser = currentUser;
    }

    @Override
    public Document viewDocument(String documentId) {
        Document doc = realService.getDocument(documentId);
        checkPermission(Action.VIEW, doc, documentId);
        return realService.viewDocument(documentId);
    }

    @Override
    public void editDocument(String documentId, String newContent) {
        Document doc = realService.getDocument(documentId);
        checkPermission(Action.EDIT, doc, documentId);
        realService.editDocument(documentId, newContent);
    }

    @Override
    public void deleteDocument(String documentId) {
        Document doc = realService.getDocument(documentId);
        checkPermission(Action.DELETE, doc, documentId);
        realService.deleteDocument(documentId);
    }

    @Override
    public List<Document> listDocuments() {
        checkPermission(Action.LIST, null, "all");
        // Filter: only return documents the user can see
        List<Document> allDocs = realService.listDocuments();
        return allDocs.stream()
                .filter(doc -> permissionManager.hasPermission(currentUser, Action.VIEW, doc))
                .toList();
    }

    public List<String> getAccessLog() {
        return Collections.unmodifiableList(accessLog);
    }

    private void checkPermission(Action action, Document doc, String resourceId) {
        boolean allowed = permissionManager.hasPermission(currentUser, action, doc);
        String logEntry = String.format("user=%s action=%s resource=%s allowed=%s",
                currentUser.getName(), action, resourceId, allowed);
        accessLog.add(logEntry);
        System.out.println("[AUDIT] " + logEntry);

        if (!allowed) {
            throw new AccessDeniedException(currentUser, action, resourceId);
        }
    }
}

// ============================================================
// CLIENT USAGE
// ============================================================

class AccessControlDemo {
    public static void main(String[] args) {
        // Setup real service with documents
        RealDocumentService realService = new RealDocumentService();
        realService.addDocument(new Document("doc1", "Public report", "user1", false));
        realService.addDocument(new Document("doc2", "Secret strategy", "user1", true));
        realService.addDocument(new Document("doc3", "Meeting notes", "user2", false));

        PermissionManager pm = new PermissionManager();

        // Viewer can view but not edit
        User viewer = new User("user3", "Alice", Role.VIEWER);
        DocumentService viewerProxy = new AccessControlProxy(realService, pm, viewer);
        viewerProxy.viewDocument("doc1");           // OK
        // viewerProxy.editDocument("doc1", "x");   // throws AccessDeniedException

        // Editor can edit non-confidential docs
        User editor = new User("user2", "Bob", Role.EDITOR);
        DocumentService editorProxy = new AccessControlProxy(realService, pm, editor);
        editorProxy.editDocument("doc3", "Updated"); // OK (owns doc3)
        // editorProxy.viewDocument("doc2");          // denied (confidential)

        // Admin has full access
        User admin = new User("admin1", "Charlie", Role.ADMIN);
        DocumentService adminProxy = new AccessControlProxy(realService, pm, admin);
        adminProxy.viewDocument("doc2");             // OK (admin)
        adminProxy.deleteDocument("doc1");           // OK (admin)
    }
}
```

### Interview Talking Points

- **Why not just put permission checks in the service?** Violates SRP — service should handle document CRUD, not auth. Proxy keeps concerns separated and is testable independently.
- **Real-world parallel:** Spring Security's `@PreAuthorize` is effectively a proxy (via AOP) that checks permissions before method invocation.
- **Scaling:** In production, PermissionManager would query a policy engine (e.g., OPA, Casbin) rather than an in-memory map.
