---
title: "Processing Json Data With Python"
date: 2023-02-21T06:53:40+09:00
draft: true
tags: [json,python,pandas]
---

# tips - pretty printing JSON
Ref: https://stackoverflow.com/questions/352098/how-can-i-pretty-print-json-in-a-shell-script
- Install: `apt install jq`
- Usecases:
  - `cat XXXX.json | jq .`
  - `jq --color-output . file1.json file1.json | less -R`

## Multiple JSON chunks to DataFrame
```python
import json
import pandas as pd

file_path = 'test.json'
json_data = []
with open(file_path, 'r') as f:
    for line in f:
        json_data.append(json.loads(line))

json_data2 = []
for datum in json_data:
    new = []
    new.append(datum["a"])
    new.append(datum["b"]["b-3"])
    new.append(datum["c"])
    json_data2.append(new)

df2 = pd.DataFrame(json_data2)
df2.columns = ["AA", "BB", "CC"]
print(df2["CC"].value_counts(normalize=True))


```
