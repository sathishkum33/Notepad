<!DOCTYPE html>
<html>
<head>
    <title>Image Preview</title>
</head>
<body>
    <button id="previewBtn">Preview</button>
    <img id="generatedImage" style="display:none; max-width: 100%;"/>
    
    <script>
        document.getElementById("previewBtn").onclick = function () {
            const data = { text: "Hello, World!" }; // Example JSON

            fetch("/generate-image", {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify(data)
            })
            .then(response => response.blob())
            .then(imageBlob => {
                const imgURL = URL.createObjectURL(imageBlob);
                const imgElement = document.getElementById("generatedImage");
                imgElement.src = imgURL;
                imgElement.style.display = "block";
            })
            .catch(error => console.error("Error:", error));
        };
    </script>
</body>
</html>