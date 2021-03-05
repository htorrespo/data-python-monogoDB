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

## Principal Component Analysis

Principal Component Analysis (PCA) finds the principal components of data. Principal components represent the underlying structure in the data because they uncover the directions where the data has the most variance (most spread out). PCA leverages eigenvectors and eigenvalues to uncover data variance. An eigenvector is a direction, while an eigenvalue is a number that indicates variance (in the data) in the direction of the eigenvector. The eigenvector with the highest eigenvalue is the principal component. A dataset can be deconstructed into eigenvectors and eigenvalues. The amount of eigenvectors (and eigenvalues) in a dataset equals the number of dimensions. Since the wrangled.json dataset has four dimensions (variables), it has four eigenvectors/eigenvalues.

The 1st code example runs PCA on the wrangled.json dataset. However, PCA only works with numeric data, so the dataset is distilled down to only those features.

```python
import matplotlib.pyplot as plt, pandas as pd
import numpy as np, json, random as rnd
from sklearn.preprocessing import StandardScaler
from pandas.plotting import parallel_coordinates

def read_json(f):
    with open(f) as f:
        return json.load(f)

def unique_features(k, d):
    return list(set([dic[k] for dic in d]))

def sire_features(k, d):
    return [{k: row[k] for k in (k)} for row in d]

def sire_numeric(k, d):
    s = conv_float(sire_features(k, d))
    return s

def sire_sample(k, v, d, m):
    indices = np.arange(0, len(d), 1)
    s = [d[i] for i in indices if d[i][k] == v]
    n = len(s)
    num_keys = ['sale', 'quan', 'disc', 'prof']
    for i, row in enumerate(s):
        for k in num_keys:
            row[k] = float(row[k])
    s = rnd_sample(m, len(s), s)
    return (s, n)

def rnd_sample(m, n, d):
    indices = sorted(rnd.sample(range(n), m))
    return [d[i] for i in indices]

def conv_float(d):
    return [dict([k, float(v)] for k, v in row.items()) for row in d]

if __name__ == "__main__":
    f = 'data/wrangled.json'
    data = read_json(f)
    segm = unique_features('segm', data)
    print ('classes in "segm" feature:')
    print (segm)
    keys = ['sale', 'quan', 'disc', 'prof', 'segm']
    features = sire_features(keys, data)
    num_keys = ['sale', 'quan', 'disc', 'prof']
    numeric_data = sire_numeric(num_keys, features)
    k, v = "segm", "Home Office"
    m = 100
    s_home = sire_sample(k, v, features, m)
    v = "Consumer"
    s_cons = sire_sample(k, v, features, m)
    v = "Corporate"
    s_corp = sire_sample(k, v, features, m)
    print ('\nHome Office slice:', s_home[1])
    print('Consumer slice:', s_cons[1])
    print ('Coporate slice:', s_corp[1])
    print ('sample size:', m)
    df_home = pd.DataFrame(s_home[0])
    df_cons = pd.DataFrame(s_cons[0])
    df_corp = pd.DataFrame(s_corp[0])
    frames = [df_home, df_cons, df_corp]
    result = pd.concat(frames)
    plt.figure()
    parallel_coordinates(result, 'segm', color=
                         ['orange','lime','fuchsia'])
    df = pd.DataFrame(numeric_data)
    X = df.ix[:].values
    X_std = StandardScaler().fit_transform(X)
    mean_vec = np.mean(X_std, axis=0)
    cov_mat = np.cov(X_std.T)
    print ('\ncovariance matrix:\n', cov_mat)
    eig_vals, eig_vecs = np.linalg.eig(cov_mat)
    print ('\nEigenvectors:\n', eig_vecs)
    print ('\nEigenvalues:\n', np.sort(eig_vals)[::-1])
    tot = sum(eig_vals)
    var_exp = [(i / tot)*100 for i in sorted(eig_vals, reverse=True)]
    print ('\nvariance explained:\n', var_exp)
    corr_mat = np.corrcoef(X.T)
    print ('\ncorrelation matrix:\n', corr_mat)    
    eig_vals, eig_vecs = np.linalg.eig(corr_mat)
    print ('\nEigenvectors:\n', eig_vecs)
    print ('\nEigenvalues:\n', np.sort(eig_vals)[::-1])
    tot = sum(eig_vals)
    var_exp = [(i / tot)*100 for i in sorted(eig_vals, reverse=True)]
    print ('\nvariance explained:\n', var_exp)
    cum_var_exp = np.cumsum(var_exp)
    fig, ax = plt.subplots()
    labels = ['PC1', 'PC2', 'PC3', 'PC4']
    width = 0.35
    index = np.arange(len(var_exp))
    ax.bar(index, var_exp,
           color=['fuchsia', 'lime', 'thistle', 'thistle'])
    for i, v in enumerate(var_exp):
        v = round(v, 2)
        val = str(v) + '%'
        ax.text(i, v+0.5, val, ha="center", color="b",
                fontsize=9, fontweight="bold")
    plt.xticks(index, labels)
    plt.title('Variance Explained')
    plt.show()
```

Open image in new window

Open image in new windowFigure 6-2

Figure 6-2 Parallel coordinates

Open image in new window

Open image in new windowFigure 6-3

Figure 6-3 Variance explained

The code example begins by importing matplotlib, pandas, numpy, json, random, and sklearn libraries. Function read_json() reads a JSON file. Function unique_features() distills unique categories (classes) from a dimension (feature). In this case, it distills three classes—Home Office, Corporate, and Consumer—from the segm feature. Since the dataset is close to 10,000 records, I wanted to be sure what classes are in it. Function sire_features() distills a new dataset with only features of interest. Function sire_numeric() converts numeric strings to float. Function sire_sample() returns a random sample of n records filtered for a class. Function rnd_sample() creates a random sample. Function convert_float() converts numeric string data to float.

The main block begins by reading wrangled.json and creating dataset features with only features of interest. The code continues by creating dataset numeric that only includes features with numeric data. Dataset numeric is used to generate PCA. Next, three samples of size 100 are created; one for each class. The samples are used to create the parallel coordinates visualization (Figure 6-2). Code for PCA follows by standardizing and transforming the numeric dataset. A covariance matrix is created so that eigenvectors and eigenvalues can be generated. I include PCA using the correlation matrix because some disciplines prefer it. Finally, a visualization of the principal components is created.

Parallel coordinates show that prof (profit) and sale (sales) are the most important features. The PCA visualization (Figure 6-3) shows that the 1st principal component accounts for 39.75%, 2nd 26.47%, 3rd 22.03%, and 4th 11.75%. PCA analysis is not very useful in this case, since all four principal components are necessary, especially the 1st three. So, we cannot drop any of the dimensions from future analysis.

The 2nd code example uses the iris dataset for PCA:

```python
import matplotlib.pyplot as plt, pandas as pd, numpy as np
from sklearn.preprocessing import StandardScaler
from pandas.plotting import parallel_coordinates

def conv_float(d):
    return d.astype(float)

if __name__ == "__main__":
    df = pd.read_csv('data/iris.csv')
    X = df.ix[:,0:4].values
    y = df.ix[:,4].values
    X_std = StandardScaler().fit_transform(X)
    mean_vec = np.mean(X_std, axis=0)
    cov_mat = np.cov(X_std.T)
    eig_vals, eig_vecs = np.linalg.eig(cov_mat)
    print ('Eigenvectors:\n', eig_vecs)
    print ('\nEigenvalues:\n', eig_vals)
    plt.figure()
    parallel_coordinates(df, 'Name', color=
                         ['orange','lime','fuchsia'])
    tot = sum(eig_vals)
    var_exp = [(i / tot)*100 for i in sorted(eig_vals, reverse=True)]
    cum_var_exp = np.cumsum(var_exp)
    fig, ax = plt.subplots()
    labels = ['PC1', 'PC2', 'PC3', 'PC4']
    width = 0.35
    index = np.arange(len(var_exp))
    ax.bar(index, var_exp,
           color=['fuchsia', 'lime', 'thistle', 'thistle'])
    for i, v in enumerate(var_exp):
        v = round(v, 2)
        val = str(v) + '%'
        ax.text(i, v+0.5, val, ha="center", color="b",
                fontsize=9, fontweight="bold")
    plt.xticks(index, labels)
    plt.title('Variance Explained')
    plt.show()
```

Open image in new window

Open image in new windowFigure 6-4

Figure 6-4 Parallel coordinates

Open image in new windowFigure 6-5

Figure 6-5 Variance explained

The code example is much shorter than the previous one, because we didn’t have to wrangle , clean (as much), and create random samples (for Parallel Coordinates visualization). The code begins by importing matplotlib, pandas, numpy, and sklearn libraries. Function conv_float() converts numeric strings to float. The main block begins by reading the iris dataset. It continues by standardizing and transforming the data for PCA. Parallel Coordinates and variance explained are then displayed.

Parallel Coordinates shows that PetalLength and PetalWidth are the most important features (Figure 6-4). The PCA visualization (Variance Explained) shows that the 1st principal component accounts for 72.77%, 2nd 23.03%, 3rd 3.68%, and 4th 0.52% (Figure 6-5). PCA analysis is very useful in this case because the 1st two principal components account for over 95% of the variance. So, we can drop PC3 and PC4 from further consideration.

For clarity, the 1st step for PCA is to explore the eigenvectors and eigenvalues. The eigenvectors with the lowest eigenvalues bear the least information about the distribution of the data, so they can be dropped. In this example, the 1st two eigenvalues are much higher, especially PC1. Dropping PC3 and PC4 are thereby in order. The 2nd step is to measure explained variance, which can be calculated from the eigenvalues. Explained variance tells us how much information (variance) can be attributed to each of the principal components. Looking at explained variance confirms that PC3 and PC4 are not important.

## Speed Simulation

Speed in data science is important, especially as datasets become bigger. Generators are helpful in memory optimization, because a generator function returns one item at a time (as needed) rather than all items at once.

The code example contrasts speed between a list and a generator:

```python
import json, humanfriendly as hf
from time import clock

def read_json(f):
    with open(f) as f:
        return json.load(f)

def mk_gen(k, d):
    for row in d:
        dic = {}
        for key in k:
            dic[key] = float(row[key])
        yield dic

def conv_float(keys, d):
    return [dict([k, float(v)] for k, v in row.items()
                 if k in keys) for row in d]

if __name__ == "__main__":
    f = 'data/wrangled.json'
    data = read_json(f)
    keys = ['sale', 'quan', 'disc', 'prof']
    print ('create, convert, and display list:')
    start = clock()
    data = conv_float(keys, data)
    for i, row in enumerate(data):
        if i < 5:
            print (row)
    end = clock()
    elapsed_ls = end - start
    print (hf.format_timespan(elapsed_ls, detailed=True))
    print ('\ncreate, convert, and display generator:')
    start = clock()
    generator = mk_gen(keys, data)
    for i, row in enumerate(generator):
        if i < 5:
            print (row)
    end = clock()
    elapsed_gen = end - start
    print (hf.format_timespan(elapsed_gen, detailed=True))
    speed = round(elapsed_ls / elapsed_gen, 2)
    print ('\ngenerator is', speed, 'times faster')
```

Open image in new window 

The code example begins by importing json, humanfriendly, and time libraries. You may have to install humanfriendly like I did as so: pip install humanfriendly. Function read_json() reads JSON. Function mk_gen() creates a generator based on four features from wrangled.json and converts values to float. Function conv_float() converts dictionary values from a list to float. The main block begins by reading wrangled.json into a list. The code continues by timing the process of creating a new list from keys and converting values to float. Next, a generator is created that mimics the list creating and conversion process. The generator is 2.26 times faster (on my computer).

## Big Data

Big data is the rage of the 21st century. So, let’s work with a relatively big dataset. GroupLens is a website that offers access to large social computing datasets for theory and practice. GroupLens has collected and made available rating datasets from the MovieLens website:

https://grouplens.org/datasets/movielens/ . We are going to explore the 1M dataset, which contains approximately one million ratings from six thousand users on four thousand movies. I was hesitant to wrangle, cleanse, and process a dataset over one million because of the limited processing power of my relatively new PC.

The 1st code example reads, cleans, sizes, and dumps MovieLens data to JSON:

```python
import json, csv

def read_dat(h, f):
    return csv.DictReader((line.replace('::', ':')
                           for line in open(f)),
                          delimiter=':', fieldnames=h,
                          quoting=csv.QUOTE_NONE)

def gen_dict(d):
    for row in d:
        yield dict(row)

def dump_json(f, l, d):
    f = open(f, 'w')
    f.write('[')
    for i, row in enumerate(d):
        j = json.dumps(row)
        f.write(j)
        if i < l - 1:
            f.write(',')
        else:
            f.write(']')
    f.close()

def read_json(f):
    with open(f) as f:
        return json.load(f)

def display(n, f):
    for i, row in enumerate(f):
        if i < n:
            print (row)
    print()

if __name__ == "__main__":
    print ('... sizing data ...\n')
    u_dat = 'data/ml-1m/users.dat'
    m_dat = 'data/ml-1m/movies.dat'
    r_dat = 'data/ml-1m/ratings.dat'
    unames = ['user_id', 'gender', 'age', 'occupation', 'zip']
    mnames = ['movie_id', 'title', 'genres']
    rnames = ['user_id', 'movie_id', 'rating', 'timestamp']
    users = read_dat(unames, u_dat)
    ul = len(list(gen_dict(users)))
    movies = read_dat(mnames, m_dat)
    ml = len(list(gen_dict(movies)))
    ratings = read_dat(rnames, r_dat)
    rl = len(list(gen_dict(ratings)))
    print ('size of datasets:')
    print ('users', ul)
    print ('movies', ml)
    print ('ratings', rl)
    print ('\n... dumping data ...\n')
    users = read_dat(unames, u_dat)
    users = gen_dict(users)
    movies = read_dat(mnames, m_dat)
    movies = gen_dict(movies)
    ratings = read_dat(rnames, r_dat)
    ratings = gen_dict(ratings)
    uf = 'data/users.json'
    dump_json(uf, ul, users)
    mf = 'data/movies.json'
    dump_json(mf, ml, movies)
    rf = 'data/ratings.json'
    dump_json(rf, rl, ratings)
    print ('\n... verifying data ...\n')
    u = read_json(uf)
    m = read_json(mf)
    r = read_json(rf)
    n = 1
    display(n, u)
    display(n, m)
    display(n, r)
```

Open image in new window

The code example begins by importing json and csv libraries. Function read_dat() reads and cleans the data (replaces double colons with single colons as delimiters). Function gen_dict() converts an OrderedDict list to a regular dictionary list for easier processing. Function dump_json() is a custom function that I wrote to dump data to JSON. Function read_json() reads JSON. Function display() displays some data for verification. The main block begins by reading the three datasets and finding their sizes. It continues by rereading the datasets and dumping to JSON. The datasets need to be reread, because a generator can only be traversed once. Since the ratings dataset is over one million records, it takes a few seconds to process.

The 2nd code example cleans the movie dataset , which requires extensive additional cleaning:

```pythoh
import json, numpy as np

def read_json(f):
    with open(f) as f:
        return json.load(f)

def dump_json(f, d):
    with open(f, 'w') as fout:
        json.dump(d, fout)    

def display(n, d):
    [print (row) for i,row in enumerate(d) if i < n]

def get_indx(k, d):
    return [row[k] for row in d if 'null' in row]

def get_data(k, l, d):
    return [row for i, row in enumerate(d) if row[k] in l]

def get_unique(key, d):
    s = set()
    for row in d:
        for k, v in row.items():
            if k in key:
                s.add(v)
    return np.sort(list(s))

if __name__ == "__main__":
    mf = 'data/movies.json'
    m = read_json(mf)
    n = 20
    display(n, m)
    print ()
    indx = get_indx('movie_id', m)
    for row in m:
        if row['movie_id'] in indx:
            row['title'] = row['title'] + ':' + row['genres']
            row['genres'] = row['null'][0]
            del row['null']
        title = row['title'].split(" ")
        year = title.pop()
        year = ''.join(c for c in year if c not in '()')
        row['title'] = ' '.join(title)
        row['year'] = year
    data = get_data('movie_id', indx, m)
    n = 2
    display(n, data)
    s = get_unique('year', m)
    print ('\n', s, '\n')
    rec = get_data('year', ['Assignment'], m)
    print (rec[0])
    rec = get_data('year', ["L'Associe1982"], m)
    print (rec[0], '\n')
    b1, b2, cnt = False, False, 0
    for row in m:
        if row['movie_id'] in ['1001']:
            row['year'] = '1982'
            print (row)
            b1 = True
        elif row['movie_id'] in ['2382']:
            row['title'] = 'Police Academy 5: Assignment: Miami Beach'
            row['genres'] = 'Comedy'
            row['year'] = '1988'
            print (row)
            b2 = True
        elif b1 and b2: break
        cnt += 1
    print ('\n', cnt, len(m))
    mf = 'data/cmovies.json'    
    dump_json(mf, m)
    m = read_json(mf)
    display(n, m)
```

Open image in new window

The code example begins by importing json and numpy libraries. Function read_json() reads JSON. Function dump_json() saves JSON. Function display() displays n records. Function get_indx() returns indices of dictionary elements with a null key. Function get_data() returns a dataset filtered by indices and movie_id key. Function get_unique() returns a list of unique values from a list of dictionary elements. The main block begins by reading movies.json and displaying for inspection. Records 12 and 19 have a null key. The code continues by finding all movie_id indices with a null key. The next several lines clean all movies. Those with a null key require added logic to fully clean, but all records have modified titles and a new year key. To verify, records 12 and 19 are displayed. To be sure that all is well, the code finds all unique keys based on year. Notice that there are two records that don’t have a legitimate year. So, the code cleans the two records. The 2nd elif was added to the code to stop processing once the two dirty records were cleaned. Although not included in the code, I checked movie_id, title, and genres keys but found no issues.

The code to connect to MongoDB is as follows:

```python
class conn:
    from pymongo import MongoClient
    client = MongoClient('localhost', port=27017)
    
    def __init__(self, dbname):
        self.db = conn.client[dbname]
    
    def getDB(self):
        return self.db
```

I created directory ‘classes’ and saved the code in ‘conn.py’

The 3rd code example generates useful information from the three datasets:

```python
import json, numpy as np, sys, os, humanfriendly as hf
from time import clock
sys.path.append(os.getcwd()+'/classes')
import conn

def read_json(f):
    with open(f) as f:
        return json.load(f)

def get_column(A, v):
    return [A_i[v] for A_i in A]

def remove_nr(v1, v2):
    set_v1 = set(v1)
    set_v2 = set(v2)
    diff = list(set_v1 - set_v2)
    return diff

def get_info(*args):
    a = [arg for arg in args]
    ratings = [int(row[a[0][1]]) for row in a[2] if row[a[0][0]] == a[1]]
    uids = [row[a[0][3]] for row in a[2] if row[a[0][0]] == a[1]]
    title = [row[a[0][2]] for row in a[3] if row[a[0][0]] == a[1]]
    age = [int(row[a[0][4]]) for col in uids for row in a[4] if col == row[a[0][3]]]
    gender = [row[a[0][5]] for col in uids for row in users if col == row[a[0][3]]]
    return (ratings, title[0], uids, age, gender)

def generate(k, v, r, m, u):
   for i, mid in enumerate(v):
       dic = {}
       rec = get_info(k, mid, r, m, u)
       dic = {'_id':i, 'mid':mid, 'title':rec[1], 'avg_rating':np.mean(rec[0]),
              'n_ratings':len(rec[0]), 'avg_age':np.mean(rec[3]),
              'M':rec[4].count('M'), 'F':rec[4].count('F')}
       dic['avg_rating'] = round(float(str(dic['avg_rating'])[:6]),2)
       dic['avg_age'] = round(float(str(dic['avg_age'])[:6]))
       yield dic

def gen_ls(g):
    for i, row in enumerate(g):
        yield row

if __name__ == "__main__":
    print ('... creating datasets ...\n')
    m = 'data/cmovies.json'
    movies = np.array(read_json(m))
    r = 'data/ratings.json'
    ratings = np.array(read_json(r))
    r = 'data/users.json'
    users = np.array(read_json(r))
    print ('... creating movie indicies vector data ...\n')
    mv = get_column(movies, 'movie_id')
    rv = get_column(ratings, 'movie_id')
    print ('... creating unrated movie indicies vector ...\n')
    nrv = remove_nr(mv, rv)
    diff = [int(row) for row in nrv]
    print (np.sort(diff), '\n')
    new_mv = [x for x in mv if x not in nrv]
    mid = '1'
    keys = ('movie_id', 'rating', 'title', 'user_id', 'age', 'gender')
    stats = get_info(keys, mid, ratings, movies, users)
    avg_rating = np.mean(stats[0])
    avg_age = np.mean(stats[3])
    n_ratings = len(stats[0])
    title = stats[1]
    M, F = stats[4].count('M'), stats[4].count('F')
    print ('avg rating for:', end=' "')
    print (title + '" is', round(avg_rating, 2), end=' (')
    print (n_ratings, 'ratings)\n')
    gen = generate(keys, new_mv, ratings, movies, users)
    gls = gen_ls(gen)
    obj = conn.conn('test')
    db = obj.getDB()
    movie_info = db.movie_info
    movie_info.drop()
    print ('... saving movie_info to MongoDB ...\n')
    start = clock()
    for row in gls:
        movie_info.insert(row)
    end = clock()
    elapsed_ls = end - start
    print (hf.format_timespan(elapsed_ls, detailed=True))
```

Open image in new window

The code example begins by importing json, numpy, sys, os, humanfriendly, time, and conn (a custom class I created to connect to MongoDB). Function read_json() reads JSON. Function get_column() returns a column vector. Function remove_nr() removes movie_id values that are not rated. Function get_info() returns ratings, users, age, and gender as column vectors as well as title of a movie. The function is very complex, because each vector is created by traversing one of the data sets and making comparisons. To make it more concise, list comprehension was used extensively. Function generate() generates a dictionary element that contains average rating, average age, number of males and females raters, number of ratings, movie_id, and title of each movie. Function gen_ls() generates each dictionary element generated by function generate(). The main block begins by reading the three JSON datasets. It continues by getting two column vectors—each movie_id from movies dataset and movie_id from ratings dataset. Each column vector is converted to a set to remove duplicates. Column vectors are used instead of full records for faster processing. Next, a new column vector is returned containing only movies that are rated. The code continues by getting title and column vectors for ratings, and users, age, and gender for each movie with movie_id of 1. The average rating for this movie is displayed with its title and number of ratings. The final part of the code creates a generator containing a list of dictionary elements. Each dictionary element contains the movie_id, title, average rating, average age, number of ratings, number of male raters, and number of female raters. Next, another generator is created to generate the list. Creating the generators is instantaneous, but unraveling (unfolding) contents takes time. Keep in mind that the 1st generator runs billions of processes and 2nd generator runs the 1st one. So, saving contents to MongoDB takes close to half an hour.

To verify results, let’s look at the data in MongoDB. The command show collections is the 1st that I run to check if collection movie_info was created:

```
> show collections
movie_info
```

Next, I run db.movie_info.count() to check the number of documents:

```
db.movie_info.count()
3706
```

Now that I know the number of documents, I can display the first and last five records:

```
db.movie_info.find({}, {title: 1}).limit(5)
```

```
db.movie_info.find({}, {title: 1}).skip(3701)
```

From data exploration, it appears that the movie_info collection was created correctly.

The 4th code example saves the three datasets—users.json, cmovies.json, and ratings.json—to MongoDB :

```python
import sys, os, json, humanfriendly as hf
from time import clock
sys.path.append(os.getcwd() + '/classes')
import conn

def read_json(f):
    with open(f) as f:
        return json.load(f)

def create_db(c, d):
    c = db[c]
    c.drop()
    for i, row in enumerate(d):
        row['_id'] = i
        c.insert(row)

if __name__ == "__main__":
    u = read_json('data/users.json')
    m = read_json('data/cmovies.json')
    r = read_json('data/ratings.json')
    obj = conn.conn('test')
    db = obj.getDB()
    print ('... creating MongoDB collections ...\n')
    start = clock()
    create_db('users', u)
    create_db('movies', m)
    create_db('ratings', r)
    end = clock()
    elapsed_ls = end - start
    print (hf.format_timespan(elapsed_ls, detailed=True))
```

Open image in new window

The code example begins by importing sys, os, json, humanfriendly, time, and custom class conn. Function read_json reads JSON. Function create_db() creates MongoDB collections. The main block begins by reading the three datasets—users.json, cmovies.json, and ratings.json—and saving them to MongoDB collections. Since the ratings.json dataset is over one million records, it takes some time to save it to the database.

The 5th code example introduces the aggregation pipeline, which is a MongoDB framework for data aggregation modeled on the concept of data processing pipelines. Documents enter a multistage pipeline that transforms them into aggregated results. In addition to grouping and sorting documents by specific field or fields and aggregating contents of arrays, pipeline stages can use operators for tasks such as calculating averages or concatenating strings . The pipeline provides efficient data aggregation using native MongoDB operations, and is the preferred method for data aggregation in MongoDB.

```python
import sys, os
sys.path.append(os.getcwd() + '/classes')
import conn

def match_item(k, v, d):
    pipeline = [ {'$match' : { k : v }} ]
    q = db.command('aggregate',d,pipeline=pipeline)
    return q

if __name__ == "__main__":
    obj = conn.conn('test')
    db = obj.getDB()
    movie = 'Toy Story'
    q = match_item('title', movie, 'movie_info')
    r = q['result'][0]
    print (movie, 'document:')
    print (r)
    print ('average rating', r['avg_rating'], '\n')
    user_id = '3'
    print ('*** user', user_id, '***')
    q = match_item('user_id', user_id, 'users')
    r = q['result'][0]    
    print ('age', r['age'], 'gender', r['gender'], 'occupation',\
          r['occupation'], 'zip', r['zip'], '\n')
    print ('*** "user 3" movie ratings of 5 ***')
    q = match_item('user_id', user_id, 'ratings')
    mid = q['result']
    for row in mid:
        if row['rating'] == '5':
            q = match_item('movie_id', row['movie_id'], 'movies')
            title = q['result'][0]['title']
            genre = q['result'][0]['genres']
            print (row['movie_id'], title, genre)
    mid = '1136'
    q = match_item('mid', mid, 'movie_info')
    title = q['result'][0]['title']
    avg_rating = q['result'][0]['avg_rating']
    print ()
    print ('"' + title + '"', 'average rating:', avg_rating)
```

Open image in new window

The code example begins by importing sys, os, and custom class conn. Function match_item() uses the aggregation pipeline to match records to criteria. The main block begins by using the aggregation pipeline to return the Toy Story document from collection movie_info. The code continues by using the pipeline to return the user 3 document from collection users. Next, the aggregation pipeline is used to return all movie ratings of 5 for user 3. Finally, the pipeline is used to return the average rating for Monty Python and the Holy Grail from collection movie_info. The aggregation pipeline is efficient and offers a vast array of functionality.

The 6th code example demonstrates a multistage aggregation pipeline :

```python
import sys, os
sys.path.append(os.getcwd() + '/classes')
import conn

def stages(k, v, r, d):
    pipeline = [ {'$match' : { '$and' : [ { k : v },
                   {'rating':{'$eq':r} }] } },
                 {'$project' : {
                     '_id' : 1,
                     'user_id' : 1,
                     'movie_id' : 1,
                     'rating' : 1 } },
                 {'$limit' : 100}]
    q = db.command('aggregate',d,pipeline=pipeline)
    return q

def match_item(k, v, d):
    pipeline = [ {'$match' : { k : v }} ]
    q = db.command('aggregate',d,pipeline=pipeline)
    return q

if __name__ == "__main__":
    obj = conn.conn('test')
    db = obj.getDB()
    u = '3'
    r = '5'
    q = stages('user_id', u, r, 'ratings')
    result = q['result']
    print ('ratings of', r, 'for user ' + str(u) + ':')
    for i, row in enumerate(result):
        print (row)
    n = i+1
    print ()
    print (n, 'associated movie titles:')
    for i, row in enumerate(result):
        q = match_item('movie_id', row['movie_id'], 'movies')
        r = q['result'][0]
        print (r['title'])
```

Open image in new window

The code example begins by importing sys, os, and custom class conn. Function stages() uses a three-stage aggregation pipeline . The 1st stage finds all ratings of 5 from user 3. The 2nd stage projects the fields to be displayed. The 3rd stage limits the number of documents returned. It is important to include a limit stage, because the results database is big and pipelines have size limitations. Function match_item() uses the aggregation pipeline to match records to criteria. The main block begins by using the stages() pipeline to return all ratings of 5 from user 3. The code continues by iterating this data and using the match_item() pipeline to get the titles that user 3 rated as 5. The pipeline is an efficient method to query documents from MongoDB, but takes practice to get acquainted with its syntax.

## Twitter

Twitter is a fantastic source of data because you can get data about almost anything. To access data from Twitter, you need to connect to the Twitter Streaming API. Connection requires four pieces of information from Twitter—API key, API secret, Access token, and Access token secret (encrypted). After you register and get your credentials, you need to install a Twitter API. I chose the Twitter API TwitterSearch, but there are many others.

The 1st code example creates JSON to hold my Twitter credentials (insert your credentials into each variable):

```python
import json

if __name__ == '__main__':
    consumer_key = ''
    consumer_secret = ''
    access_token = ''
    access_encrypted = ''
    data = {}
    data['ck'] = consumer_key
    data['cs'] = consumer_secret
    data['at'] = access_token
    data['ae'] = access_encrypted
    json_data = json.dumps(data)
    header = '[\n'
    ender = ']'
    obj = open('data/credentials.json', 'w')
    obj.write(header)
    obj.write(json_data + '\n')
    obj.write(ender)
    obj.close()  
```

I chose to save credentials in JSON to hide them from view. The code example imports the json library. The main block saves credentials into JSON.

The 2nd code example streams Twitter data using the TwitterSearch API. To install: pip install TwitterSearchAPI .

```python
from TwitterSearch import *
import json, sys

class twitSearch:
    
    def __init__(self, cred, ls, limit):
        self.cred = cred
        self.ls = ls
        self.limit = limit
    
    def search(self):
        num = 0
        dt = []
        dic = {}
        try:
            tso = TwitterSearchOrder()
            tso.set_keywords(self.ls)
            tso.set_language('en')
            tso.set_include_entities(False)
            ts = TwitterSearch(
                consumer_key = self.cred[0]['ck'],
                consumer_secret = self.cred[0]['cs'],
                access_token = self.cred[0]['at'],
                access_token_secret = self.cred[0]['ae']
                )
            for tweet in ts.search_tweets_iterable(tso):
                if num <= self.limit:
                    dic['_id'] = num
                    dic['tweeter'] = tweet['user']['screen_name']
                    dic['tweet_text'] = tweet['text']
                    dt.append(dic)
                    dic = {}
                else:
                    break
                num += 1
        except TwitterSearchException as e:
            print (e)
        return dt

def get_creds():
    with open('data/credentials.json') as json_data:
        d = json.load(json_data)
        json_data.close()
    return d

def write_json(f, d):
    with open(f, 'w') as fout:
        json.dump(d, fout)

def translate():
    return dict.fromkeys(range(0x10000, sys.maxunicode + 1), 0xfffd)

def read_json(f):
    with open(f) as f:
        return json.load(f)

if __name__ == '__main__':
    cred = get_creds()
    ls = ['machine', 'learning']
    limit = 10
    obj = twitSearch(cred, ls, limit)
    data = obj.search()
    f = 'data/TwitterSearch.json'
    write_json(f, data)
    non_bmp_map = translate()
    print ('twitter data:')
    for row in data:
        row['tweet_text'] = str(row['tweet_text']).translate(non_bmp_map)
        tweet_text = row['tweet_text'][0:50]
        print ('{:<3}{:18s}{}'.format(row['_id'], row['tweeter'], tweet_text))
    print ('\nverify JSON:')
    read_data = read_json(f)
    for i, p in enumerate(read_data):
        if i < 3:
            p['tweet_text'] = str(p['tweet_text']).translate(non_bmp_map)
            tweet_text = p['tweet_text'][0:50]
            print ('{:<3}{:18s}{}'.format(p['_id'], p['tweeter'], tweet_text))
```

Open image in new window

The code example begins by importing TwitterSearch, json, and sys libraries. Class twitSearch streams Twitter data based on Twitter credentials, a list of keywords, and a limit. Function get_cred() returns Twitter credentials from JSON. Function write_json() writes data to JSON. Function translate() converts streamed data outside the Basic Multilingual Plane (BMP) to a usable format. Emojis, for example, are outside the BMP. Function read_json() reads JSON. The main block begins by getting Twitter credentials, creating a list of search keywords, and a limit. In this case, the list of search keywords holds machine and learning, because I wanted to stream data about machine learning. Limit of ten restricts streamed records to ten tweets. The code continues by writing Twitter data to JSON, translating tweets to control for non-BMP data, and printing the tweet. Finally, the code reads JSON to verify that the tweets were saved properly and prints a few.

## Web Scraping

Web scraping is a programmatic approach for extracting information from websites. It focuses on transforming unstructured HTML formatted data into structured data. Web scraping is programmatically intensive because of the unstructured nature of HTML. That is, HTML has few if any structural rules, which means that HTML structural patterns tend to differ from one website to another. So, get ready to write custom code for each Web scraping adventure.

The code example scrapes book information from a popular technical book publishing company. The 1st step is to locate the webpage. The 2nd step is to open a window with the source code. The 3rd step is to traverse the source code to identify the data to scrape. The 4th step is to scrape.

With Google Chrome, click More tools and then Developer tools to open the source code window. Next, hover the mouse cursor over the source until you find the data. Move down the source code tree to find the tags you want to scrape. Finally, scrape the data.

To install ‘BeautifulSoup’, pip intall BeautifulSoup .

```python
from bs4 import BeautifulSoup
import requests, json

def build_title(t):
    t = t.text
    t = t.split()
    ls = []
    for row in t:
        if row != '-':
            ls.append(row)
        elif row == '-':
            break
    return ' '.join(ls)

def release_date(r):
    r = r.text
    r = r.split()
    prefix = r[0] + s + r[1]
    if len(r) == 5:
        date = r[2] + s + r[3] + s + r[4]
    else:
        date = r[2] + s + r[3]
    return prefix, date        

def write_json(f, d):
    with open(f, 'w') as fout:
        json.dump(d, fout)

def read_json(f):
    with open(f) as f:
        return json.load(f)

if __name__ == '__main__':
    s = ' '
    dic_ls = []
    base_url = "https://ssearch.oreilly.com/?q=data+science"
    soup = BeautifulSoup(requests.get(base_url).text, 'lxml')
    books = soup.find_all('article')
    for i, row in enumerate(books):
        dic = {}
        tag = row.name
        tag_val = row['class']
        title = row.find('p', {'class' : 'title'})
        title = build_title(title)
        url = row.find('a', {'class' : 'learn-more'})
        learn_more = url.get('href')
        author = row.find('p', {'class' : 'note'}).text
        release = row.find('p', {'class' : 'note date2'})
        prefix, date = release_date(release)
        if len(tag_val) == 2:
            publisher = row.find('p', {'class' : 'note publisher'}).text
            item = row.find('img', {'class' : 'book'})
            cat = item.get('class')[0]
        else:
            publisher, cat = None, None
            desc = row.find('p', {'class' : 'description'}).text.split()
            desc = [row for i, row in enumerate(desc) if i < 7]
            desc = ' '.join(desc) + ' ...'
        dic['title'] = title
        dic['learn_more'] = learn_more
        if author[0:3] != 'Pub':
            dic['author'] = author
        if publisher is not None:
            dic['publisher'] = publisher
            dic['category'] = cat
        else:
            dic['event'] = desc
        dic['date'] = date
        dic_ls.append(dic)
    f = 'data/scraped.json'
    write_json(f, dic_ls)
    data = read_json(f)
    for i, row in enumerate(data):
        if i < 6:
            print (row['title'])
            if 'author' in row.keys():
                print (row['author'])
            if 'publisher' in row.keys():
                print (row['publisher'])
            if 'category' in row.keys():
                print ('Category:', row['category'])
                print ('Release Date:', row['date'])
            if 'event' in row.keys():
                print ('Event:', row['event'])
                print ('Publish Date:', row['date'])
            print ('Learn more:', row['learn_more'])
            print ()
```

Open image in new window

The code example begins by importing BeautifulSoup, request, and json libraries. Function build_title() builds scraped title data into a string. Function release_date() builds scraped date data into a string. Function write_json() and read_json() write and read JSON respectively. The main block begins by converting the URL page into a BeautifulSoup object. The code continues by placing all article tags into variable books. From exploration, I found that the article tags contained the information I wanted to scrape. Next, each article tag is traversed. Scraping would have been much easier if the information in each article tag was structured consistently. Since it was not, the logic to extract each piece of information is extensive. Each piece of information is placed in a dictionary element, which is subsequently appended to a list. Finally, the list is saved to JSON. The JSON is read and a few records are displayed to verify that all is well.

