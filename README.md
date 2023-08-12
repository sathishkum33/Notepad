import sqlfluff.api

sql_file_path = 'your_sql_file.sql'

with open(sql_file_path, 'r') as sql_file:
    sql_code = sql_file.read()

try:
    violations = sqlfluff.api.lint_string(sql_code)
    if not violations:
        print("No syntax errors or linting issues found.")
    else:
        for v in violations:
            print(v)
except Exception as e:
    print("An error occurred:", e)
