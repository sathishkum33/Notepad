from django.http import HttpResponse, Http404
from django.views.decorators.csrf import csrf_exempt
import json
import os

@csrf_exempt  # Use only in development; for production, use CSRF tokens as shown above
def get_svg(request):
    if request.method == "POST":
        data = json.loads(request.body)
        filename = data.get("filename", "output.svg")

        # Adjust this path to your actual SVG directory
        file_path = os.path.join("media/generated", filename)

        if os.path.exists(file_path):
            with open(file_path, "r", encoding="utf-8") as f:
                svg_content = f.read()
            return HttpResponse(svg_content, content_type="image/svg+xml")
        else:
            raise Http404("SVG file not found")