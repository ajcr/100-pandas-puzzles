# 100 pandas puzzles

Inspired by [100 Numpy exerises](https://github.com/rougier/numpy-100), here are 100* short puzzles for testing your knowledge of [pandas'](http://pandas.pydata.org/) power.

Since pandas is a large library with many different specialist features and functions, these excercises focus mainly on the fundamentals of manipulating data (indexing, grouping, aggregating, cleaning), making use of the core DataFrame and Series objects. Many of the excerises here are stright-forward in that the solutions require no more than a few lines of code (in pandas or NumPy - don't go using pure Python or Cython!). Choosing the right methods and following best practices is the underlying goal.

The exercises are loosely divided in sections. Each section has a difficulty rating; these ratings are subjective, of course, but should be a seen as a rough guide as to how elaborate a solution is required.

The official documentation for pandas is very useful and very extensive. Good places get a broader overview of pandas are

- [10 minutes to pandas](http://pandas.pydata.org/pandas-docs/version/0.17.0/10min.html)
- [pandas basics](http://pandas.pydata.org/pandas-docs/version/0.17.0/basics.html)
- [tutorials](http://pandas.pydata.org/pandas-docs/stable/tutorials.html)
- [cookbook and idioms](http://pandas.pydata.org/pandas-docs/version/0.17.0/cookbook.html#cookbook)

\* the list of exercises is not complete! Pull requests or suggestions for additional exercises, corrections and improvements are welcomed.

---

### Importing pandas

Getting started and checking your pandas setup.

Difficulty: *easy*

**1.** Import pandas under the name `pd`.

``` python
import pandas as pd
```

**2.** Print the version of pandas that has been imported.

``` python
pd.__version__
```

**3.** Print out all the version information of the libraries that are required by the pandas library.

``` python
pd.show_versions()
```

---

### DataFrame basics

A few of the fundamental routines for selecting, sorting, adding and aggregating data in DataFrames.

Note: remember to import numpy using `import numpy as np`

Difficulty: *easy*

Consider the following Python dictionary `data` and list `labels`:

``` python
data = {'animal': ['cat', 'cat', 'snake', 'dog', 'dog', 'cat', 'snake', 'cat', 'dog', 'dog'],
        'age': [2.5, 3, 0.5, np.nan, 5, 2, 4.5, np.nan, 7, 3],
        'visits': [1, 3, 2, 3, 2, 3, 1, 1, 2, 1],
        'priority': ['yes', 'yes', 'no', 'yes', 'no', 'no', 'no', 'yes', 'no', 'no']}

labels = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j']
```

**4.** Create a DataFrame `df` from this dictionary `data` which has the index `labels`.

``` python
df = pd.DataFrame(data, index=labels)
```

**5.** Display a summary of the basic information about this DataFrame and its data.

``` python
df.info()

# and also

df.describe()
```

**6.** Return the first 3 rows of the DataFrame `df`.

```python
df.iloc[:3]

# or equivalently

df.head(3)
```

**7.** Select just the 'animal' and 'age' columns.

```python
df.loc[:, ['animal', 'age']]

# or

df[['animal', 'age']]
```

**8.** Select the data in rows `[3, 4, 8]` *and* in columns `['animal', 'age']`.

```python
df.ix[[3, 4, 8], ['animal', 'age']]
```

**9.** Select the rows where the number of visits is greater than 3.

```python
df[df['visits'] > 3]
```

**10.** Select the rows where the age is missing, i.e. is `NaN`.

```python
df[df['age'].isnull()]
```

**10.** Select the rows where the animal is a cat *and* the age is less than 3.

```python
df[(df['animal'] == 'cat') & (df['age'] < 3)]
```

**11.** Select the rows the age is between 2 and 4 (inclusive).

```
df[df['age'].between(2, 4)]
```

**12.** Change the age in row 'f' to 1.5.

```python
df.loc['f', 'age'] = 1.5
```

**13.** Calculate the sum of all visits.

```python
df['visits'].sum()
```

**14.** Calculate the mean age for each different animal in `df`.

```python
df.groupby('animal')['age'].mean()
```

**15.** Append a new row 'k' to `df` with your choice of values for each column. Now delete that row.

``` python
df.loc['k'] = [5.5, 'dog', 'no', 2]

# and then...

df = df.drop('k')
```

**16.** Count the number of each type of animal in `df`.

```python
df['animal'].value_counts()
```

**17.** Sort `df` first by the values in the 'age' in decending order, then by the value in the 'visit' column in ascending order.

``` python
df.sort_values(by=['age', 'visits'], ascending=[False, True])
```

**18.** The 'priority' column contains the values 'yes' and 'no'. Replace this column with a column of boolean values: 'yes' should be `True` and 'no' should be `False`.

``` python
df['priority'] = df['priority'].map({'yes': True, 'no': False})
```

**19.** In the 'animal' column, change the 'snake' entries to 'python'.

``` python
df['animal'] = df['animal'].replace('snake', 'python')
```

**20.** For each animal type and each number of visits, find the mean age (hint: use a pivot table).

``` python
df.pivot_table(index='animal', columns='visits', values='age', aggfunc='mean')
```

### DataFrames: beyond the basics

These problems are just beyond the most basic operations on DataFrames. You may need to combine two or more methods to get the right answer.

Difficulty: *medium*


**21.** You have a DataFrame `df` with a column 'A' of integers. How do you filter out rows which contain the same integer as the row immediately above?

```python
df.loc[df['A'].shift() != df['A']]
```

**22.** Given a DataFrame of numeric values, how do you subtract the row mean from each element in the row?

```python
df.sub(df.mean(axis=1), axis=0)
```

**23.** Your DataFrame has 10 columns of real numbers. Which column has the smallest sum?

```python
df.sum().idxmin()
```

**24.** Count how many unique rows a DataFrame has (i.e. ignore all rows that have duplicates).

``` python
len(df) - df.duplicated(keep=False).sum()

# or simply...

len(df.drop_duplicates(keep=False))
```

**25.** You have a DataFrame that consists of 10 float columns. Exactly 5 entries in each row are NaN values. For each row of the DataFrame, find the column which contains the *third* NaN value.

```python
(df.isnull().cumsum(axis=1) == 3).idxmax(axis=1)
```

**26.** A DataFrame has a column of groups 'grps' and and column of numbers 'vals'. For each group, find the sum of the three greatest values.

```python
df = df.sort_values('vals', ascending=False)
df.groupby('grp')['vals'].nlargest(3).sum(level=0)

# or in one line using `apply`...

df.groupby('grp')['vals'].apply(lambda x: pd.Series(x).nlargest(3).sum())
```

**27.** A DataFrame has two integer columns 'A' and 'B'. The values in 'A' are between 1 and 100 (inclusive). For each group of 10 consecutive integers in 'A' (i.e. `(0, 10]`, `(10, 20]`, ...), calculate the sum of the corresponding values in column 'B'.

```python
df.groupby(pd.cut(df['A'], np.arange(0, 101, 10)))['B'].sum()
```


---


### DataFrames: harder problems 

These are problems that might require a bit of thinking outside the box. All are solvable using pandas/NumPy methods.

Difficulty: *hard*

**28.** In a DataFrame `df` there is an integer column 'X' with the values `[7, 2, 0, 3, 4, 2, 5, 0, 3, 4]`. For each value, count the difference back to the previous zero (or the start of the Series, whichever is closer); these values should therefore be `[1, 2, 0, 1, 2, 3, 4, 0, 1, 2]`. Make this a new column 'Y'.

``` python
izero = np.r_[-1, (df['X'] == 0).nonzero()[0]] # indices of zeros
idx = np.arange(len(df))
df['Y'] = idx - izero[np.searchsorted(izero - 1, idx) - 1]

# http://stackoverflow.com/questions/30730981/how-to-count-distance-to-the-previous-zero-in-pandas-series/
# credit: Behzad Nouri
```
Alternative approach based on a [cookbook recipe](http://pandas.pydata.org/pandas-docs/stable/cookbook.html#grouping):

``` python
x = (df['X'] != 0).cumsum()
y = x != x.shift()
df['Y'] = y.groupby((y != y.shift()).cumsum()).cumsum()
```

**29.** Consider a DataFrame containing rows and columns of purely numerical data. Create a list of the row-column index locations of the 3 largest values.

```python
df.unstack().sort_values()[-3:].index.tolist()


# http://stackoverflow.com/questions/14941261/index-and-column-for-the-max-value-in-pandas-dataframe/
# credit: DSM
```

**30.** Given a DataFrame with a column of group IDs, 'grps', and a column of corresponding integer values, 'vals', replace any negative values in 'vals' with the group mean.

```python
def replace(group):
    mask = group<0
    group[mask] = group[~mask].mean()
    return group

df.groupby(['grps'])['vals'].transform(replace)

# http://stackoverflow.com/questions/14760757/replacing-values-with-groupby-means/
# credit: unutbu
```

---

### Series and DatetimeIndex

Exercises for creating and manipulating Series with datetime data.

Difficulty: easy/medium

**31.** Create a DatetimeIndex that contains each business day of 2015 and use it to index a Series of random numbers. Let `s` be the variable identifying this Series.

``` python
dti = pd.date_range(start='2015-01-01', end='2015-12-31', freq='B') 
s = pd.Series(np.random.rand(len(dti)), index=dti)
```

**32.** Find the sum of the values in `s` for every Wednesday.

```python
s[dti.weekday == 2].sum() 
```

**33.** For each calendar month in `s`, find the mean of values.

```python
s.resample('M', how='mean')
```

**34.** For each group of four consecutive calendar months in `s`, find the date on which the highest value occurred.

```python
s.groupby(pd.TimeGrouper('4M')).idxmax()
```
