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

    # This line should raise an exception if there's a syntax error
    cursor.execute(sql.SQL(sql_code))

    cursor.close()
    connection.close()
    print("No syntax errors found.")
except errors.SyntaxError as e:
    print("Syntax error detected:", e)
except Exception as e:
    print("An error occurred:", e)
