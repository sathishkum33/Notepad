<%@ Language=VBScript %>
<% Option Explicit %>
<!DOCTYPE html>
<html>
<head>
    <title>Database Connection Test</title>
</head>
<body>
<%
    Dim conn, connString
    Dim isConnected

    ' Set your database connection string
    connString = "Provider=SQLOLEDB;Data Source=your_server_name;Initial Catalog=your_database_name;User ID=your_username;Password=your_password;"

    ' Create the ADO connection object
    Set conn = Server.CreateObject("ADODB.Connection")

    ' Attempt to open the connection
    On Error Resume Next
    conn.ConnectionString = connString
    conn.Open
    If Err.Number = 0 Then
        ' Connection successful
        isConnected = True
        conn.Close ' Close the connection immediately after the test
    Else
        ' Connection failed
        isConnected = False
    End If
    On Error GoTo 0

    ' Display the result
    If isConnected Then
        Response.Write "Database connection is successful."
    Else
        Response.Write "Database connection failed. Error Message: " & Err.Description
        Err.Clear
    End If

    ' Clean up the connection object
    If Not conn Is Nothing Then
        If conn.State = 1 Then ' Check if the connection is open
            conn.Close
        End If
        Set conn = Nothing
    End If
%>
</body>
</html>
