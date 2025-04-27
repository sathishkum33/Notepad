from diagrams import Diagram
import json

# Example JSON
data = json.loads(open("input.json").read())

# Dictionary to hold created nodes
created_nodes = {}

# Dynamic import helper
def get_class(full_path):
    parts = full_path.split(".")
    module = __import__(".".join(parts[:-1]), fromlist=[parts[-1]])
    return getattr(module, parts[-1])

# Start diagram
with Diagram("Auto Generated Architecture", show=False):
    # Create nodes
    for node in data["nodes"]:
        cls = get_class(node["type"])
        created_nodes[node["id"]] = cls(node["label"])
    
    # Create edges
    for edge in data["edges"]:
        created_nodes[edge["from"]] >> created_nodes[edge["to"]]