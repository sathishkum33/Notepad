<!DOCTYPE html>
<html>
<head>
    <title>Shared Folder File Content</title>
</head>
<body>
    <h1>File Content from Shared Folder</h1>
    <pre>
        <!-- VBScript code here -->
        <%
        Set objFSO = CreateObject("Scripting.FileSystemObject")

        strServerName = "ServerName"
        strShareName = "ShareName"
        strFileName = "FileName.txt"
        strPath = "\\" & strServerName & "\" & strShareName & "\" & strFileName

        If objFSO.FileExists(strPath) Then
            Set objFile = objFSO.OpenTextFile(strPath, 1)

            Do Until objFile.AtEndOfStream
                strLine = objFile.ReadLine
                Response.Write Server.HTMLEncode(strLine) & "<br>"
            Loop

            objFile.Close
        Else
            Response.Write "File not found."
        End If
        %>
    </pre>
</body>
</html>
