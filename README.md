import sqlparse

sql_code = "SELECT first_name last_name FROM employees WHERE department_id = 5;"

parsed = sqlparse.parse(sql_code)
for statement in parsed:
    if statement.is_valid():
        print("Valid SQL:", statement)
    else:
        print("Invalid SQL:", statement)
