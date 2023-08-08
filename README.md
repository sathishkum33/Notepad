<%@ Language=VBScript %>
<!DOCTYPE html>
<html>
<head>
    <title>Print Hostname</title>
</head>
<body>
    <%
        Dim objNetwork
        Set objNetwork = CreateObject("WScript.Network")
        Response.Write "Hostname: " & objNetwork.ComputerName
        Set objNetwork = Nothing
    %>
</body>
</html>
