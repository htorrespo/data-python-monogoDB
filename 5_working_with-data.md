<!--
https://link-springer-com.ezproxy.unal.edu.co/chapter/10.1007/978-1-4842-3597-3_5
-->
# Working with Data

Working with data details the earliest processes of data science problem solving. The 1st step is to identify the problem, which determines all else that needs to be done. The 2nd step is to gather data. The 3rd step is to wrangle (munge) data, which is critical. Wrangling is getting data into a form that is useful for machine learning and other data science problems. Of course, wrangled data will probably have to be cleaned. The 4th step is to visualize the data. Visualization helps you get to know the data and, hopefully, identify patterns.

## One-Dimensional Data Example

The code example generates visualizations of two very common data distributions—uniform and normal. The uniform distribution has constant probability. That is, all events that belong to the distribution are equally probable. The normal distribution is symmetrical about the center, which means that 50% of its values are less than the mean and 50% of its values are greater than the mean. Its shape resembles a bell curve . The normal distribution is extremely important because it models many naturally occurring events.

```python
import matplotlib.pyplot as plt
import numpy as np

if __name__ == "__main__":
    plt.figure('Uniform Distribution')
    uniform = np.random.uniform(-3, 3, 1000)
    count, bins, ignored = plt.hist(uniform, 20, facecolor="lime")
    plt.xlabel('Interval: [-3, 3]')
    plt.ylabel('Frequency')
    plt.title('Uniform Distribution')
    plt.axis([-3,3,0,100])
    plt.grid(True)
    plt.figure('Normal Distribution')
    normal = np.random.normal(0, 1, 1000)
    count, bins, ignored = plt.hist(normal, 20, facecolor="fuchsia")
    plt.xlabel('Interval: [-3, 3]')
    plt.ylabel('Frequency')
    plt.title('Normal Distribution')
    plt.axis([-3,3,0,140])
    plt.grid(True)
    plt.show()
```

Open image in new windowFigure 5-1

Figure 5-1 Uniform distribution

Open image in new windowFigure 5-2

Figure 5-2 Normal distribution

The code example begins by importing matplotlib and numpy. The main block begins by creating a figure and data for a uniform distribution. Next, a histogram is created and plotted based on the data. A figure for a normal distribution is then created and plotted. See Figures 5-1 and 5-2.

## Two-Dimensional Data Example

Modeling 2-D data offers a more realistic picture of naturally occurring events. The code example compares two normally distributed distributions of randomly generated data with the same mean and standard deviation (SD). SD measures the amount of variation (dispersion) of a set of data values. Although both data sets are normally distributed with the same mean and SD, each has a very different joint distribution (correlation). Correlation is the interdependence of two variables.

```python
import matplotlib.pyplot as plt
import matplotlib.gridspec as gridspec
import numpy as np, random
from scipy.special import ndtri

def inverse_normal_cdf(r):
    return ndtri(r)

def random_normal():
    return inverse_normal_cdf(random.random())

def scatter(loc):
    plt.scatter(xs, ys1, marker='.', color="black", label="ys1")
    plt.scatter(xs, ys2, marker='.', color="gray",  label='ys2')
    plt.xlabel('xs')
    plt.ylabel('ys')
    plt.legend(loc=loc)
    plt.tight_layout()

if __name__ == "__main__":
    xs = [random_normal() for _ in range(1000)]
    ys1 = [ x + random_normal() / 2 for x in xs]
    ys2 = [-x + random_normal() / 2 for x in xs]
    gs = gridspec.GridSpec(2, 2)
    fig = plt.figure()
    ax1 = fig.add_subplot(gs[0,0])
    plt.title('ys1 data')
    n, bins, ignored = plt.hist(ys1, 50, normed=1,
                                facecolor='chartreuse', alpha=0.75)
    ax2 = fig.add_subplot(gs[0,1])
    plt.title('ys2 data')
    n, bins, ignored = plt.hist(ys2, 50, normed=1,
                                facecolor='fuchsia', alpha=0.75)
    ax3 = fig.add_subplot(gs[1,:])
    plt.title('Correlation')
    scatter(6)
    print (np.corrcoef(xs, ys1)[0, 1])
    print (np.corrcoef(xs, ys2)[0, 1])
    plt.show()
```

Open image in new window

Open image in new windowFigure 5-3

Figure 5-3 Subplot of normal distributions and correlation

The code example begins by importing matplotlib, numpy , random, and scipy libraries. Method gridspec specifies the geometry of a grid where a subplot will be placed. Method ndtri returns the standard normal cumulative distribution function (CDF). CDF is the probability that a random variable X takes on a value less than or equal to x, where x represents the area under a normal distribution. The code continues with three functions. Function inverse_normal_cdf() returns the CDF based on a random variable. Function random_normal() calls function inverse_normal_cdf() with a randomly generated value X and returns the CDF. Function scatter() creates a scatter plot. The main block begins by creating randomly generated x and y values xs, ys1, and ys2. A gridspec() is created to hold the distributions. Histograms are created for xs, ys1 and xs, ys2 data, respectively. Next, a correlation plot is created for both distributions. Finally, correlations are generated for the two distributions. Figure 5-3 shows plots.

The code example spawns two important lessons. First, creating a set of randomly generated numbers with ndtri() creates a normally distributed dataset. That is, function ndtri() returns the CDF of a randomly generated value . Second, two normally distributed datasets are not necessarily similar even though they look alike. In this case, the correlations are opposite. So, visualization and correlations are required to demonstrate the difference between the datasets.

## Data Correlation and Basic Statistics

Correlation is the extent that two or more variables fluctuate (move) together. A correlation matrix is a table showing correlation coefficients between sets of variables. Correlation coefficients measure strength of association between two or more variables.

The code example creates three datasets with x and y coordinates, calculates correlations, and plots. The 1st dataset represents a positive correlation; the 2nd, a negative correlation; and the 3rd, a weak correlation.

```python
import random, numpy as np
import matplotlib.pyplot as plt
import matplotlib.gridspec as gridspec

if __name__ == "__main__":
    np.random.seed(0)
    x = np.random.randint(0, 50, 1000)
    y = x + np.random.normal(0, 10, 1000)
    print ('highly positive:\n', np.corrcoef(x, y))
    gs = gridspec.GridSpec(2, 2)
    fig = plt.figure()
    ax1 = fig.add_subplot(gs[0,0])
    plt.title('positive correlation')
    plt.scatter(x, y, color="springgreen")
    y = 100 - x + np.random.normal(0, 10, 1000)
    print ('\nhighly negative:\n', np.corrcoef(x, y))
    ax2 = fig.add_subplot(gs[0,1])
    plt.title('negative correlation')
    plt.scatter(x, y, color="crimson")
    y = np.random.normal(0, 10, 1000)
    print ('\nno/weak:\n', np.corrcoef(x, y))
    ax3 = fig.add_subplot(gs[1,:])
    plt.title('weak correlation')
    plt.scatter(x, y, color="peachpuff")
    plt.tight_layout()
    plt.show()
```

Open image in new window

Open image in new windowFigure 5-4

Figure 5-4 Subplot of correlations

The code example begins by importing random, numpy, and matplotlib libraries. The main block begins by generating x and y coordinates with a positive correlation and displaying the correlation matrix. It continues by creating a grid to hold the subplot, the 1st subplot grid, and a scatterplot. Next, x and y coordinates are created with a negative correlation and the correlation matrix is displayed. The 2nd subplot grid is created and plotted. Finally, x and y coordinates are created with a weak correlation and the correlation matrix is displayed. The 3rd subplot grid is created and plotted, and all three scatterplots are displayed. Figure 5-4 shows the plots.

## Pandas Correlation and Heat Map Examples

Pandas is a Python package that provides fast, flexible, and expressive data structures to make working with virtually any type of data easy, intuitive, and practical in real-world data analysis. A DataFrame (df) is a 2-D labeled data structure and the most commonly used object in pandas.

The 1st code example creates a correlation matrix with an associated visualization:

```python
import random, numpy as np, pandas as pd
import matplotlib.pyplot as plt
import matplotlib.cm as cm
import matplotlib.colors as colors

if __name__ == "__main__":
    np.random.seed(0)
    df = pd.DataFrame({'a': np.random.randint(0, 50, 1000)})
    df['b'] = df['a'] + np.random.normal(0, 10, 1000)
    df['c'] = 100 - df['a'] + np.random.normal(0, 5, 1000)
    df['d'] = np.random.randint(0, 50, 1000)
    colormap = cm.viridis
    colorlist = [colors.rgb2hex(colormap(i))
                 for i in np.linspace(0, 1, len(df['a']))]
    df['colors'] = colorlist
    print (df.corr())
    pd.plotting.scatter_matrix(df, c=df['colors'], diagonal="d",
                               figsize=(10, 6))
    plt.show()
```

Open image in new window

Open image in new windowFigure 5-5

Figure 5-5 Correlation matrix visualization

The code example begins by importing random, numpy, pandas, and matplotlib libraries. The main block begins by creating a df with four columns populated by various random number possibilities. It continues by creating a color map of the correlations between each column, printing the correlation matrix, and plotting the color map (Figure 5-5).

We can see from the correlation matrix that the most highly correlated variables are a and b (0.83), a and c (–0.95), and b and c (–0.79). From the color map, we can see that a and b are positively correlated, a and c are negatively correlated, and b and c are negatively correlated. However, the actual correlation values are not apparent from the visualiztion.

A Heat map is a graphical representation of data where individual values in a matrix are represented as colors. It is a popular visualization technique in data science. With pandas, a Heat map provides a sophisticated visualization of correlations where each variable is represented by its own color.

The 2nd code example uses a Heat map to visualize variable correlations. You need to install library seaborn if you don’t already have it installed on your computer (e.g., pip install seaborn).

```python
import random, numpy as np, pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

if __name__ == "__main__":
    np.random.seed(0)
    df = pd.DataFrame({'a': np.random.randint(0, 50, 1000)})
    df['b'] = df['a'] + np.random.normal(0, 10, 1000)
    df['c'] = 100 - df['a'] + np.random.normal(0, 5, 1000)
    df['d'] = np.random.randint(0, 50, 1000)
    plt.figure()
    sns.heatmap(df.corr(), annot=True, cmap="OrRd")
    plt.show()
```

Open image in new windowFigure 5-6

Figure 5-6 Heat map

The code begins by importing random, numpy, pandas , matplotlib, and seaborn libraries. Seaborn is a Python visualization library based on matplotlib. The main block begins by generating four columns of data (variables), and plots a Heat map (Figure 5-6). Attribute cmap uses a colormap. A list of matplotlib colormaps can be found at: https://matplotlib.org/examples/color/colormaps_reference.html .

## Various Visualization Examples

The 1st code example introduces the Andrews curve, which is a way to visualize structure in high-dimensional data. Data for this example is the Iris dataset, which is one of the best known in the pattern recognition literature. The Iris dataset consists of three different types of irises’ (Setosa, Versicolour, and Virginica) petal and sepal lengths.

Andrews curves allow multivariate data plotting as a large number of curves that are created using the attributes (variable) of samples as coefficients. By coloring the curves differently for each class, it is possible to visualize data clustering. Curves belonging to samples of the same class will usually be closer together and form larger structures. Raw data for the iris dataset is located at the following URL:

https://raw.githubusercontent.com/pandas-dev/pandas/master/pandas/tests/data/iris.csv

```python
import matplotlib.pyplot as plt
import pandas as pd
from pandas.plotting import andrews_curves

if __name__ == "__main__":
    data = pd.read_csv('data/iris.csv')
    plt.figure()
    andrews_curves(data, 'Name',
                   color=['b','mediumspringgreen','r'])
    plt.show()
```

Open image in new windowFigure 5-7

Figure 5-7 Andrews curves

The code example begins by importing matplotlib and pandas . The main block begins by reading the iris dataset into pandas df data. Next, Andrews curves are plotted for each class—Iris-setosa, Iris-versicolor, and Iris-virginica (Figure 5-7). From this visualization, it is difficult to see which attributes distinctly define each class.

The 2nd code example introduces parallel coordinates :

```python
import matplotlib.pyplot as plt
import pandas as pd
from pandas.plotting import parallel_coordinates

if __name__ == "__main__":
    data = pd.read_csv('data/iris.csv')
    plt.figure()
    parallel_coordinates(data, 'Name',
                         color=['b','mediumspringgreen','r'])
    plt.show()
```

Open image in new windowFigure 5-8

Figure 5-8 Parallel coordinates

Parallel coordinates is another technique for plotting multivariate data. It allows visualization of clusters in data and estimation of other statistics visually. Points are represented as connected line segments. Each vertical line represents one attribute. One set of connected line segments represents one data point. Points that tend to cluster appear closer together.

The code example begins by importing matplotlib and pandas. The main block begins by reading the iris dataset into pandas df data. Next, parallel coordinates are plotted for each class (Figure 5-8). From this visualization, attributes PetalLength and PetalWidth are most distinct for the three species (classes of Iris). So, PetalLength and PetalWidth are the best classifiers for species of Iris. Andrews curves just don’t clearly provide this important information.

Here is a useful URL:

http://wilkelab.org/classes/SDS348/2016_spring/worksheets/class9.html

The 3rd code example introduces RadViz :

```python
import matplotlib.pyplot as plt
import pandas as pd
from pandas.plotting import radviz

if __name__ == "__main__":
    data = pd.read_csv('data/iris.csv')
    plt.figure()
    radviz(data, 'Name',
           color=['b','mediumspringgreen','r'])
    plt.show()
```

Open image in new windowFigure 5-9

Figure 5-9 RadVis

RadVis is yet another technique for visualizing multivariate data. The code example begins by importing matplotlib and pandas. The main block begins by reading the iris dataset into pandas df data. Next, RadVis coordinates are plotted for each class (Figure 5-9). With this visualization, it is not easy to see any distinctions. So, the parallel coordinates technique appears to be the best of the three in terms of recognizing variation (for this example).

## Cleaning a CSV File with Pandas and JSON

The code example loads a dirty CSV file into a Pandas df and displays to locate bad data. It then loads the same CSV file into a list of dictionary elements for cleaning. Finally, the cleansed data is saved to JSON.

```python
import csv, pandas as pd, json

def to_dict(d):
    return [dict(row) for row in d]

def dump_json(f, d):
    with open(f, 'w') as f:
        json.dump(d, f)

def read_json(f):
    with open(f) as f:
        return json.load(f)

if __name__ == "__main__":
    df = pd.read_csv("data/audio.csv")
    print (df, '\n')
    data = csv.DictReader(open('data/audio.csv'))
    d = to_dict(data)
    
    for row in d:
        if (row['pno'][0] not in ['a', 'c', 'p', 's']):
            if (row['pno'][0] == '8'):
                row['pno'] = 'a' + row['pno']
            elif (row['pno'][0] == '7'):
                row['pno'] = 'p' + row['pno']
            elif (row['pno'][0] == '5'):
                row['pno'] = 's' + row['pno']
        if (row['color']) == '-':
            row['color'] = 'silver'
        if row['model'] == '-':
            row['model'] = 'S1'
        if (row['mfg']) == '100':
            row['mfg'] = 'Linn'
        if (row['desc'] == '0') and row['pno'][0] == 'p':
            row['desc'] = 'preamplifier'
        elif (row['desc'] == '-') and row['pno'][0] == 's':
            row['desc'] = 'speakers'
        if (row['price'][0] == '$'):
            row['price'] =\
            row['price'].translate({ord(i): None for i in '$,.'})
    
    json_file = 'data/audio.json'
    dump_json(json_file, d)
    data = read_json(json_file)
    for i, row in enumerate(data):
        if i < 5:
            print (row)
```

Open image in new window

The code example begins by importing csv, pandas, and json libraries. Function to_dict() converts a list of OrderedDict elements to a list of regular dictionary elements for easier processing. Function dump_json() saves data to a JSON file. Function read_json() reads JSON data into a Python list. The main block begins by loading a CSV file into a Pandas df and displaying it to visualize dirty data. It continues by loading the same CSV file into a list of dictionary elements for easier cleansing. Next, all dirty data is cleansed. The code continues by saving the cleansed data to JSON file audio.json. Finally, audio.json is loaded and a few records are displayed to ensure that everything worked properly.

## Slicing and Dicing

Slicing and dicing is breaking data into smaller parts or views to better understand and present it as information in a variety of different and useful ways. A slice in multidimensional arrays is a column of data corresponding to a single value for one or more members of the dimension of interest. While a slice filters on a particular attribute, a dice is like a zoom feature that selects a subset of all dimensions, but only for specific values of the dimension .

The code example loads audio.json into a Pandas df, slices data by column and row, and displays:

```python
import pandas as pd

if __name__ == "__main__":
    df = pd.read_json("data/audio.json")    
    amps = df[df.desc == 'amplifier']
    print (amps, '\n')
    price = df.query('price >= 40000')
    print (price, '\n')
    between = df.query('4999 < price < 6000')
    print (between, '\n')
    row = df.loc[[0, 10, 19]]
    print (row)  
```

Open image in new window

The code example begins by importing Pandas. The main block begins by loading audio.json into a Pandas df. Next, the df is sliced by amplifier from the desc column . The code continues by slicing by the price column for equipment more expensive than $40,000. The next slice is by price column for equipment between $5,000 and $6,000. The final slice is by rows 0, 10, and 19.

