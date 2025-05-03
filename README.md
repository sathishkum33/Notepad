<!DOCTYPE html>
<html>
<head>
    <title>SVG Preview</title>
</head>
<body>
    <button id="previewBtn">Preview SVG</button>
    <div id="svgContainer"></div>

    <script>
        document.getElementById("previewBtn").onclick = function () {
            const jsonData = { text: "Hello SVG!" };

            fetch("/get-svg/", {
                method: "POST",
                headers: { "Content-Type": "application/json", "X-CSRFToken": getCSRFToken() },
                body: JSON.stringify(jsonData)
            })
            .then(response => response.text())
            .then(svgContent => {
                document.getElementById("svgContainer").innerHTML = svgContent;
            });

            // Helper to get CSRF token
            function getCSRFToken() {
                const match = document.cookie.match(/csrftoken=([^;]+)/);
                return match ? match[1] : '';
            }
        };
    </script>
</body>
</html>