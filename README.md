import pkgutil
import diagrams

# walks the diagrams namespace and returns every module or package
all_paths = [
    name
    for _, name, ispkg in pkgutil.walk_packages(diagrams.__path__, prefix="diagrams.")
]

print(f"Found {len(all_paths)} module paths:")
for path in all_paths:
    print(path)