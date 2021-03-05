<!--
https://link-springer-com.ezproxy.unal.edu.co/chapter/10.1007/978-1-4842-3597-3_6
-->

# Exploring Data

Exploring probes deeper into the realm of data. An important topic in data science is dimensionality reduction. This chapter borrows munged data from Chapter  5 to demonstrate how this works. Another topic is speed simulation. When working with large datasets, speed is of great importance. Big data is explored with a popular dataset used by academics and industry. Finally, Twitter and Web scraping are two important data sources for exploration.

## Heat Maps

Heat maps were introduced in Chapter  5, but one wasn’t created for the munged dataset. So, we start by creating a Heat map visualization of the wrangled.json data.

```python
import json, pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

def read_json(f):
    with open(f) as f:
        return json.load(f)

def verify_keys(d, **kwargs):
    data = d[0].items()
    k1 = set([tup[0] for tup in data])
    s = kwargs.items()
    k2 = set([tup[1] for tup in s])
    return list(k1.intersection(k2))

def build_ls(k, d):
    return [{k: row[k] for k in (keys)} for row in d]

def get_rows(d, n):
    [print(row) for i, row in enumerate(d) if i < n]

def conv_float(d):
    return [dict([k, float(v)] for k, v in row.items()) for row in d]

if __name__ == "__main__":
    f= 'data/wrangled.json'
    data = read_json(f)
    keys = verify_keys(data, c1="sale", c2="quan", c3="disc", c4="prof")
    heat = build_ls(keys, data)
    print ('1st row in "heat":')
    get_rows(heat, 1)
    heat = conv_float(heat)
    print ('\n1st row in "heat" converted to float:')
    get_rows(heat, 1)
    df = pd.DataFrame(heat)
    plt.figure()
    sns.heatmap(df.corr(), annot=True, cmap="OrRd")
    plt.show()
```

Open image in new window

Open image in new windowFigure 6-1

Figure 6-1 Heat map

The code example begins by importing json, pandas, matplotlib, and seaborn libraries. Function read_json() reads a JSON file. Function verify_keys() ensures that the keys of interest exist in the JSON file. This is important because we can only create a Heat map based on numerical variables, and the only candidates from the JSON file are sales, quantity, discount, and profit. Function build_ls() builds a list of dictionary elements based on the numerical variables. Function get_rows() returns n rows from a list. Function conv_float() converts dictionary elements to float. The main block begins by reading JSON file wrangled.json. It continues by getting keys for only numerical variables. Next, it builds list a list of dictionary elements (heat) based on the appropriate keys. The code displays the 1st row in heat to verify that all values are float. Since they are not, the code converts them to float. The code then creates a df from heat and plots the Heat map (Figure 6-1).

