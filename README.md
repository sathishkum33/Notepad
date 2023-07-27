<%@ Language=VBScript %>
<% Option Explicit %>
<!DOCTYPE html>
<html>
<head>
    <title>SQL Query Command Timeout</title>
</head>
<body>
<%
    Dim conn, cmd, rs
    Dim connectionString, query

    ' Set your database connection string
    connectionString = "Provider=MSOLEDBSQL;Server=your_server_name;Database=your_database_name;UID=your_username;PWD=your_password;"

    ' Set the SQL query that you want to execute
    query = "SELECT * FROM YourTableName;"

    ' Create the ADO connection, command, and recordset objects
    Set conn = Server.CreateObject("ADODB.Connection")
    Set cmd = Server.CreateObject("ADODB.Command")
    Set rs = Server.CreateObject("ADODB.Recordset")

    ' Open the connection
    conn.ConnectionString = connectionString
    conn.Open

    ' Set up the command object
    cmd.ActiveConnection = conn
    cmd.CommandText = query

    ' Set the command timeout (in seconds)
    cmd.CommandTimeout = 30 ' Set the command timeout to 30 seconds

    ' Execute the query and handle any errors
    On Error Resume Next
    rs.Open cmd
    If Err.Number <> 0 Then
        Response.Write "Error Message: " & Err.Description
        Err.Clear
    Else
        ' Process the query results here if needed
        Do While Not rs.EOF
            ' Code to process the data, if any
            Response.Write rs("ColumnName") & "<br>"
            rs.MoveNext
        Loop
        rs.Close
    End If
    On Error GoTo 0

    ' Clean up and close the connection
    If Not rs Is Nothing Then
        rs.Close
        Set rs = Nothing
    End If

    If Not cmd Is Nothing Then
        Set cmd = Nothing
    End If

    If Not conn Is Nothing Then
        If conn.State = 1 Then ' Check if the connection is open
            conn.Close
        End If
        Set conn = Nothing
    End If
%>
</body>
</html>
