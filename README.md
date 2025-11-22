# Comfy Gizmo Ignorifier

[Comfy Gizmo Ignorifier](https://dsthedev.github.io/ComfyGizmoIgnorifier/) is a tool to bulk manage which items are ignored by the wonderful [Comfy Gizmo](https://thunderstore.io/c/valheim/p/ComfyMods/Gizmo/) mod; since not everything needs to be rotated on all axis.

No more horizontal cauldrons or upside down chairs, sorry!

## Instructions & Disclaimer

This tool is provided as-is. Use it at your own discretion. Intended for players or mod authors working with Valheim piece lists.

To generate the YML files needed for this tool, run the following devcommand within Valheim with the WackyDB mod installed:

`wackydb_all_pieces`

That will create many `Piece_*.yml` files into `wackyDatabase-BulkYML/Pieces`. Run the Python script below in that folder to generate a CSV for uploading here.

From this tool, you can copy the generated string into the `ignorePrefabNameList` section of the Comfy Gizmo config file.

---

Python script to generate a new csv file:

```
import os
import csv
import yaml
from collections import defaultdict

def load_yaml_file(path):
    with open(path, "r", encoding="utf-8") as f:
        return yaml.safe_load(f)

def main():
    folder = os.getcwd()
    categories = defaultdict(list)
    for filename in os.listdir(folder):
        if filename.lower().endswith(".yml"):
            full_path = os.path.join(folder, filename)
            try:
                data = load_yaml_file(full_path)
                name = data.get("name")
                category = data.get("piecehammerCategory", "Uncategorized")
                if name:
                    categories[category].append(name)
            except Exception as e:
                print(f"Error reading {filename}: {e}")
    rows = []
    for category in sorted(categories.keys(), key=str.lower):
        for piece in sorted(categories[category], key=str.lower):
            rows.append({"category": category, "piece": piece})
    output_path = os.path.join(folder, "valPiecesByCategory.csv")
    with open(output_path, "w", newline="", encoding="utf-8") as csvfile:
        writer = csv.DictWriter(csvfile, fieldnames=["category", "piece"])
        writer.writeheader()
        writer.writerows(rows)
    print(f"CSV file created: {output_path}")

if __name__ == "__main__":
    main()
```
