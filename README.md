import psycopg2
from psycopg2 import sql
from psycopg2 import errors

db_params = {
    'dbname': 'your_db_name',
    'user': 'your_username',
    'password': 'your_password',
    'host': 'your_host',
    'port': 'your_port',
}

sql_file_path = 'your_sql_file.sql'

with open(sql_file_path, 'r') as sql_file:
    sql_code = sql_file.read()

try:
    connection = psycopg2.connect(**db_params)
    cursor = connection.cursor()

    # Split SQL statements into individual queries
    sql_statements = sql_code.split(';')

    for statement in sql_statements:
        try:
            if statement.strip():  # Avoid empty statements
                cursor.execute(sql.SQL(statement))
        except (errors.SyntaxError, Exception) as e:
            print("Error:", e)
            print("Query:", statement)
            print("=" * 40)

    cursor.close()
    connection.close()
except Exception as e:
    print("An error occurred:", e)
