# Ticket Status History Tracking in PostgreSQL

## Overview
This guide provides a PostgreSQL setup for tracking the full status history of tickets. Every status change is logged in a separate table, ensuring a complete history of updates.

---

## Steps to Implement
### 1. Create the `ticket_status_history` Table
This table stores all status updates for every ticket.

```sql
CREATE TABLE ticket_status_history (
    id SERIAL PRIMARY KEY,
    ticket_id INT NOT NULL,
    status VARCHAR(50) NOT NULL,
    changed_by INT NULL,  -- User ID (if applicable)
    changed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

- `ticket_id`: The ID of the ticket being modified.
- `status`: The status assigned to the ticket.
- `changed_by`: The user who made the change (optional).
- `changed_at`: Timestamp of the change.

---

### 2. Create the Logging Function
This function logs every status update into the history table.

```sql
CREATE OR REPLACE FUNCTION log_ticket_status_history()
RETURNS TRIGGER AS $$
BEGIN
    -- Insert new status into history table
    INSERT INTO ticket_status_history (ticket_id, status, changed_by, changed_at)
    VALUES (NEW.id, NEW.status, NEW.updated_by, NOW());

    RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```

---

### 3. Create the Trigger
This trigger executes the function every time a status update occurs.

```sql
CREATE TRIGGER trigger_ticket_status_history
AFTER UPDATE OF status ON ticket
FOR EACH ROW
EXECUTE FUNCTION log_ticket_status_history();
```

---

## Querying the Status History
To retrieve the full history of a specific ticket:

```sql
SELECT * FROM ticket_status_history WHERE ticket_id = 1 ORDER BY changed_at DESC;
```

---

## Notes
- Ensure the `ticket` table has an `updated_by` column if you want to track the user making changes.
- This setup guarantees a complete audit trail for status changes.

---

Now, every status change is stored, maintaining a full history for better tracking and analysis. 🎯

