<%@ Language=VBScript %>
<% Option Explicit %>
<!DOCTYPE html>
<html>
<head>
    <title>Query Timeout Simulation</title>
</head>
<body>
<%
    Dim conn, rs, cmd, query
    Dim connectionString, connectionTimeout

    ' Set your database connection string
    connectionString = "Provider=SQLOLEDB;Data Source=your_server_name;Initial Catalog=your_database_name;User ID=your_username;Password=your_password;"

    ' Set the connection timeout value (in seconds)
    ' For simulation purposes, set a low timeout value, e.g., 5 seconds
    connectionTimeout = 5

    ' SQL query that will take a long time to execute intentionally
    query = "WAITFOR DELAY '00:00:20'; SELECT * FROM YourTableName;"

    ' Create the ADO connection and command objects
    Set conn = Server.CreateObject("ADODB.Connection")
    Set cmd = Server.CreateObject("ADODB.Command")

    ' Open the connection with the specified timeout
    conn.ConnectionString = connectionString
    conn.CommandTimeout = connectionTimeout ' Setting the connection timeout here
    conn.Open

    ' Set up the command object
    cmd.ActiveConnection = conn
    cmd.CommandText = query

    ' Execute the query and handle any errors
    On Error Resume Next
    Set rs = cmd.Execute
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

    If Not conn Is Nothing Then
        If conn.State = 1 Then ' Check if the connection is open
            conn.Close
        End If
        Set conn = Nothing
    End If
%>
</body>
</html>

