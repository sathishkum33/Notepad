<%@ Language=VBScript %>
<!DOCTYPE html>
<html>
<head>
    <title>Print FQDN</title>
</head>
<body>
    <%
        Dim objNetwork
        Set objNetwork = CreateObject("WScript.Network")
        Dim strHostName
        strHostName = objNetwork.ComputerName
        
        Dim objWMIService, colItems, objItem
        Set objWMIService = GetObject("winmgmts:\\.\root\cimv2")
        Set colItems = objWMIService.ExecQuery("Select * from Win32_ComputerSystem")
        
        Dim strDomain
        For Each objItem in colItems
            strDomain = objItem.Domain
            Exit For ' We only need one result
        Next
        
        Dim strFQDN
        If strDomain <> "" Then
            strFQDN = strHostName & "." & strDomain
        Else
            strFQDN = strHostName
        End If
        
        Response.Write "FQDN: " & strFQDN
        
        Set objNetwork = Nothing
    %>
</body>
</html>
