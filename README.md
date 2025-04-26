import pkgutil
import importlib
import inspect
import json
from diagrams import Diagram, Node

# Step 1: Dynamically find all module paths inside diagrams
def get_all_diagram_modules():
    import diagrams
    return [
        name
        for _, name, ispkg in pkgutil.walk_packages(
            diagrams.__path__, prefix="diagrams."
        )
    ]

# Step 2: Build a component map (lowercase class name -> actual class)
def build_component_map():
    component_map = {}
    module_paths = get_all_diagram_modules()
    for module_path in module_paths:
        try:
            module = importlib.import_module(module_path)
            for name, cls in inspect.getmembers(module, inspect.isclass):
                if issubclass(cls, Node) and cls is not Node:
                    key = name.lower()
                    component_map[key] = cls
        except Exception as e:
            # Some modules might not import cleanly; you can log if needed
            # print(f"Failed to import {module_path}: {e}")
            continue
    return component_map

# Step 3: Load your real-time input (JSON)
def load_real_time_data():
    # Example static input for now; replace this with your actual real-time data fetch
    json_input = """
    [
        {"type": "ec2", "label": "Web Server"},
        {"type": "postgresql", "label": "Database"},
        {"type": "server", "label": "On-Prem Server"}
    ]
    """
    return json.loads(json_input)

# Step 4: Draw the diagram
def draw_diagram(components_data, component_map):
    with Diagram("Dynamic Diagram", show=False):
        for comp in components_data:
            comp_type = comp["type"].lower()
            label = comp["label"]
            if comp_type not in component_map:
                print(f"[WARN] Component type '{comp_type}' not found. Skipping.")
                continue
            CompClass = component_map[comp_type]
            CompClass(label)

# --- MAIN ENTRY ---
if __name__ == "__main__":
    # Build the component lookup dynamically
    component_map = build_component_map()

    # Load incoming component descriptions
    real_time_components = load_real_time_data()

    # Create diagram
    draw_diagram(real_time_components, component_map)

    print("Diagram creation completed.")