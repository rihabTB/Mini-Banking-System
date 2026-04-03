# Checkpoint: Concurrency Control & Distributed Database Design

## Part 1 – Transaction Management (Conceptual)

**Scenario:** Two users simultaneously initiate transfers involving the same account.

### Concurrency Issue
- Lost update: Two transactions read the same account balance, perform updates, and one update may be lost.

### Locking Mechanism
- Use **exclusive locks** on account rows during updates.
- Shared locks can be used for reading balances.

### Locking Strategy
- **Pessimistic Locking**: Ensures high accuracy for banking transactions by preventing conflicts upfront.

### Example Schedule

| Step | Transaction A       | Transaction B       | Account Balance | Safe? |
|------|-------------------|-------------------|----------------|-------|
| 1    | Read balance (1000) |                   | 1000           | Yes   |
| 2    |                   | Wait (lock)       | 1000           | Yes   |
| 3    | Subtract 100 & write (900) |           | 900            | Yes   |
| 4    | Release lock      | Read balance (900)| 900            | Yes   |
| 5    |                   | Subtract 50 & write (850) | 850   | Yes   |

---

## Part 2 – Distributed Database Planning (High-Level)

**Branches:** Tunis, Sousse, Sfax

### Horizontal Fragmentation
- Split `Customers` table by branch:
  - Tunis: `branch = 'Tunis'`
  - Sousse: `branch = 'Sousse'`
  - Sfax: `branch = 'Sfax'`
- Each branch primarily accesses local customers → better performance.

### Vertical Fragmentation
- Move `login_info` (email/password) to a separate table `CustomerLogin`.
- Isolates sensitive login data for security and access control.

### Data Replication

| Data Type           | Replicate to All Branches? | Reason                                                   |
|--------------------|---------------------------|----------------------------------------------------------|
| Customer info       | No                        | Branches mainly need local customers                    |
| Account balances    | Yes                       | Ensures consistent banking operations across branches   |
| Transaction history | Yes                       | All branches must access complete transaction history   |

### Transaction History Allocation
- **Dynamic allocation**: Database can scale and replicate automatically as history grows across branches.

---

**Summary**
- Pessimistic locking prevents lost updates in concurrent transactions.
- Horizontal and vertical fragmentation improves performance, security, and organization.
- Replication and dynamic allocation ensure data consistency and scalability across branches.
