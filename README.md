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
        Dim objShell
        Set objShell = CreateObject("WScript.Shell")
        
        Dim strHostName
        strHostName = objNetwork.ComputerName
        
        Dim strDomain
        strDomain = objShell.ExpandEnvironmentStrings("%USERDNSDOMAIN%")
        
        Dim strFQDN
        If strDomain <> "" Then
            strFQDN = strHostName & "." & strDomain
        Else
            strFQDN = strHostName
        End If
        
        Response.Write "FQDN: " & strFQDN
        
        Set objShell = Nothing
        Set objNetwork = Nothing
    %>
</body>
</html>
