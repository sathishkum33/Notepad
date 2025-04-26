import pkgutil
import importlib
import inspect
from diagrams import Node, __path__ as DIAGRAMS_PATH

def list_diagrams_modules():
    """
    Walk the `diagrams` namespace and return a list of all sub‐module names,
    e.g. "diagrams.aws.compute", "diagrams.azure.network", etc.
    """
    return [
        name
        for _, name, ispkg in pkgutil.walk_packages(DIAGRAMS_PATH, prefix="diagrams.")
        if ispkg
    ]

def list_all_components():
    """
    For each diagrams sub‐module, import it and return a dict mapping
    lowercase component names to their full class objects.
    """
    components = {}
    for module_name in list_diagrams_modules():
        module = importlib.import_module(module_name)
        for cls_name, cls_obj in inspect.getmembers(module, inspect.isclass):
            if issubclass(cls_obj, Node):
                key = cls_name.lower()
                components[key] = cls_obj
    return components

if __name__ == "__main__":
    mods = list_diagrams_modules()
    print(f"Found {len(mods)} diagrams sub‐modules:")
    for m in mods:
        print("  ", m)

    comps = list_all_components()
    print(f"\nDiscovered {len(comps)} components:")
    for name in sorted(comps):
        print("  ", name, "→", comps[name])