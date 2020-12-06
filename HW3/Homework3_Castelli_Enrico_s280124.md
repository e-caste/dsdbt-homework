# Homework 3 - Enrico Castelli s280124

## Triggers

### 1) Switching on/off the phone

```sql
CREATE OR REPLACE TRIGGER UpdateTelephone
AFTER INSERT ON STATE_CHANGE
FOR EACH ROW
WHEN (NEW.ChangeType = 'O' OR NEW.ChangeType = 'F')
DECLARE
    PhoneCellId NUMBER(38,0);
BEGIN
    -- save CellId where the phone belongs
    SELECT CellId INTO PhoneCellId
    FROM CELL
    WHERE x0<=:NEW.x AND :NEW.x<x1 AND y0<=:NEW.y AND :NEW.y<y1;
    
    IF :NEW.ChangeType = 'O' THEN
        -- add phone to TELEPHONE table
        INSERT INTO TELEPHONE (PhoneNo, X, Y, PhoneState)
        VALUES (:NEW.PhoneNo, :NEW.x, :NEW.y, 'On');
        -- increase CurrentPhone# in corresponding cell
        UPDATE CELL 
        SET CurrentPhone# = CurrentPhone# + 1 
        WHERE CellId=PhoneCellId;
    ELSE
        -- remove phone from TELEPHONE table
        DELETE FROM TELEPHONE WHERE PhoneNo=:NEW.PhoneNo;
        -- decrease CurrentPhone# in corresponding cell
        UPDATE CELL 
        SET CurrentPhone# = CurrentPhone# - 1 
        WHERE CellId=PhoneCellId;
    END IF;
END;
/
```

### 2) Starting a phone call



### 3) Changing the maximum number of active calls



### 4) Service guarantee





