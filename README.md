from sqlfluff.core import Linter

sql_file_path = 'your_sql_file.sql'

with open(sql_file_path, 'r') as sql_file:
    sql_code = sql_file.read()

linter = Linter()
violations = linter.lint_string(sql_code)

if not violations:
    print("No syntax errors or linting issues found.")
else:
    for v in violations:
        print(v)
