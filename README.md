from diagrams import Diagram, Node
import importlib, inspect

# 1. Define where to look for components in the diagrams package
MODULE_PATHS = [
    "diagrams.aws.compute",
    "diagrams.aws.database",
    "diagrams.onprem.compute",
    "diagrams.onprem.database",
    # …add any other sub‐packages you plan to use
]

def build_component_map():
    """Walk through MODULE_PATHS and map lowercase names to (module_path, class_name)."""
    comp_map = {}
    for pkg in MODULE_PATHS:
        module = importlib.import_module(pkg)
        for name, cls in inspect.getmembers(module, inspect.isclass):
            # we only want Node subclasses
            if not issubclass(cls, Node):
                continue

            key = name.lower()  # e.g. "ec2", "postgresql"
            comp_map[key] = (pkg, name)
    return comp_map

# Build once (or cache it somewhere)
COMPONENT_MAP = build_component_map()

def get_component_class(friendly_name: str):
    """
    Given a string like "ec2" or "postgres", return the actual diagrams class.
    Raises KeyError if not found.
    """
    pkg, cls_name = COMPONENT_MAP[friendly_name.lower()]
    module = importlib.import_module(pkg)
    return getattr(module, cls_name)


# --- usage example ---
# imagine you get this from your real‐time data feed:
real_time_components = [
    {"type": "ec2",       "label": "web-01"},
    {"type": "postgresql","label": "app-db"},
    {"type": "server",    "label": "legacy-box"},
]

with Diagram("Auto-Generated Topology", show=False):
    for comp in real_time_components:
        CompCls = get_component_class(comp["type"])
        CompCls(comp["label"])