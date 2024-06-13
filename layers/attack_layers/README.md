## Simple Example

The script **attack_layers_simple.py** generates layer files based on the contents of a CSV file. CSV files with pre-calculated data can be ingested and used to apply evaluation criteria - in this case it's purely a count of each detection with a specific ATT&CK technique.

It's important to emphasize that the scores generated here are **not really a good indicator over coverage of a TTP**! This formula shouldn't really be use but I am sooo lol, 

The code excerpt below shows how **attack_layers_simple.py** adds scores to techniques:

This has been modified to account for numeric values and to stop the outputting of quotes in the json which breaks the navigator

```python
# parse csv file, calculating a score for each technique and adding that to the layer
    with open(args.input_fn, "r") as csvfile:
        reader = csv.DictReader(csvfile, delimiter=",")
        for row in reader:
            # score each technique based on a simple formula
            score = int(row["Software"]) #Convert "score" to an integer (assuming it's numeric), previously this was wrapping the score in quotes which just breaks
            technique = {
                "techniqueID": row["TechID"],
                "score": score
            }

            layer_json["techniques"].append(technique)
```

**attack_layers_simple.py** adds all of the required layer fields as outlined in **LAYERFORMATv2_2.md**. Additionally, a *gradient* field is added that specifies a color range that will be applied to the techniques based on their scores. In **attack_layers_simple.py**, we specify min/max values that match the min/max of the set of technique scores that were calculated.


```python
# add a color gradient (white -> red) to layer, ranging  
# from zero (white) to the maximum score in the file (red)
layer_json["gradient"] = {
    "colors": [
        "#ffffff",   # White
        "#ff6666"    # Red
    ],
    "minValue": 0,
    "maxValue": max([technique["score"] for technique in layer_json["techniques"]])
}
```
See **attack.csv** for an example csv file that can be ingested by **attack_layers_simple.py** (simple_input.csv) and **data/samples** to view a layer file output by this code (heatmap_layer.json).
