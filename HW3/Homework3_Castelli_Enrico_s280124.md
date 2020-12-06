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

```sql
CREATE OR REPLACE TRIGGER StartPhoneCall
AFTER INSERT ON STATE_CHANGE
FOR EACH ROW
WHEN (NEW.ChangeType = 'C')
DECLARE
    PhoneCellId NUMBER(38,0);
    CellMaxCalls NUMBER(38,0);
    CellX0 NUMBER(7,2); 
    CellY0 NUMBER(7,2); 
    CellX1 NUMBER(7,2); 
    CellY1 NUMBER(7,2); 
    CurrentCellCalls NUMBER(38,0);
    LastExId NUMBER(38,0);
BEGIN
    -- save cell info to variables
    SELECT CellId, MaxCalls, x0, y0, x1, y1 INTO PhoneCellId, CellMaxCalls, CellX0, CellY0, CellX1, CellY1
    FROM CELL
    WHERE x0<=:NEW.x AND :NEW.x<x1 AND y0<=:NEW.y AND :NEW.y<y1;
    
    -- save number of active phones in cell to variable
    SELECT COUNT(*) INTO CurrentCellCalls
    FROM TELEPHONE
    WHERE PhoneState='Active' AND CellX0<=x AND x<CellX1 AND CellY0<=y AND y<CellY1;
    
    IF CurrentCellCalls < CellMaxCalls THEN
        -- phone state becomes active
        UPDATE TELEPHONE
        SET PhoneState = 'Active'
        WHERE PhoneNo=:NEW.PhoneNo;
    ELSE
        -- save biggest ExId to variable (or use 1 if no ExIds in table)
        SELECT MAX(ExId) + 1 INTO LastExId 
        FROM EXCEPTION_LOG;
        IF LastExid IS NULL THEN
            LastExid := 1;
        END IF;    
        -- insert exception in EXCEPTION_LOG
        INSERT INTO EXCEPTION_LOG (ExId, CellId, ExceptionType)
        VALUES (LastExId, PhoneCellId, 'C');
    END IF;
END;
/
```

### 3) Changing the maximum number of active calls



### 4) Service guarantee





