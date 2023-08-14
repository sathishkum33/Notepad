DO $$ 
BEGIN 
    IF NOT EXISTS (SELECT 1 FROM pg_sequences WHERE schemaname = 'public' AND sequencename = 'employee_id_seq') THEN 
        CREATE SEQUENCE employee_id_seq
            START WITH 1
            INCREMENT BY 1
            NO MINVALUE
            NO MAXVALUE
            CACHE 1; 
    END IF; 
END $$;

-- Insert a new employee using the sequence
INSERT INTO employees (emp_id, first_name, last_name, department, salary)
VALUES (nextval('employee_id_seq'), 'Emily', 'Williams', 'Marketing', 48000);
