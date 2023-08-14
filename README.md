CREATE OR REPLACE FUNCTION print_current_date()
RETURNS VOID AS $$
BEGIN
    RAISE NOTICE 'Current Date: %', NOW()::DATE;
END;
$$ LANGUAGE plpgsql;
