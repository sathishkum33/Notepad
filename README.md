import pyodbc

def fetch_table_data_and_generate_sql(server, database, username, password, table_name, output_file):
    # Establishing a connection to the MSSQL server
    conn_str = (
        f"DRIVER={{ODBC Driver 17 for SQL Server}};"
        f"SERVER={server};"
        f"DATABASE={database};"
        f"UID={username};"
        f"PWD={password};"
    )
    conn = pyodbc.connect(conn_str)
    cursor = conn.cursor()

    # Fetching data from the table
    cursor.execute(f"SELECT * FROM {table_name}")
    rows = cursor.fetchall()

    # Getting column names
    columns = [column[0] for column in cursor.description]

    # Generating SQL insert statements
    with open(output_file, 'w') as sql_file:
        for row in rows:
            values = []
            for value in row:
                if isinstance(value, str):
                    values.append(f"'{value}'")
                elif value is None:
                    values.append("NULL")
                else:
                    values.append(str(value))
            values_str = ", ".join(values)
            sql_insert = f"INSERT INTO {table_name} ({', '.join(columns)}) VALUES ({values_str});\n"
            sql_file.write(sql_insert)

    print(f"SQL file '{output_file}' generated successfully.")

# Example usage:
server = 'your_server'
database = 'your_database'
username = 'your_username'
password = 'your_password'
table_name = 'your_table'
output_file = 'output.sql'

fetch_table_data_and_generate_sql(server, database, username, password, table_name, output_file)