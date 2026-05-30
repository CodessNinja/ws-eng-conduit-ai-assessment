# Implementation Plan

## Plan

- Analyse existing article feature (create, edit, storage, API flow)
- Extend Article model to support co-authors
- Implement co-author assignment on Create Article page
  - BASIC: comma-separated email input
  - ADVANCED: multi-select user dropdown
- Update backend to store coAuthors field in article schema
- Update authorization logic so co-authors can edit articles
- Implement BASIC editing flow (last-write-wins, no locking)

### ADVANCED IMPLEMENTATION
- Implement article locking system:
  - Lock acquired when user opens edit page
  - Store lockedBy and lockTimestamp on article
  - Reject edits if article is locked by another user
- Implement lock expiry:
  - Auto-expire lock after 5 minutes of inactivity
- Add heartbeat mechanism:
  - Frontend sends periodic updates while editing
- Handle lock release on:
  - Save
  - Navigation away
  - Timeout

- Update frontend edit page:
  - Show lock status messages
  - Block editing when locked
- Add error handling for lost lock scenarios

- Test full workflow manually:
  - Create article with co-authors
  - Edit as different users
  - Validate locking behaviour

---

## Decisions

### Decision 1: Use server-side locking with timestamp expiry
- Alternative: Client-side locking only
- Alternative: WebSocket-based real-time lock sync
- Rationale:
  Server-side locking ensures correctness and prevents bypassing locks from client manipulation.

---

### Decision 2: BASIC mode uses last-write-wins conflict resolution
- Alternative: Merge conflict resolution system
- Alternative: Version history tracking
- Rationale:
  Requirement explicitly allows last saved version to be used.

---

### Decision 3: Email/user-based co-author model
- Alternative: Separate collaboration service
- Alternative: Permission-based ACL system
- Rationale:
  Simpler integration with existing user system and meets requirements.

---

## Notes

- Locking is critical for ADVANCED evaluation
- Ensure backend validates lock before saving edits
- Do not modify unrelated system features
- Focus only on Article creation and editing flows