---
layout: default
title: Data Analysis with pandas
description: Data Analysis with pandas
---

Pandas is a library for data analysis with a focus on tabular data. it is a powerful tool that not only provides many useful classes and functions but also does a great job of wrapping functionality from other packages. The result is a user interface that makes data analysis, and in particular financial analysis, a convenient and efficient task.

`DataFrame`: 2-dimensional data object with index, it's used for tabular data organized in columns
`Series`: 1-dimensional data object with index, it's used for single (time) series of data

### The DataFrame Class
At the core of pandas is the `DataFrame`, a class designed to efficiently handle data in tabular form—i.e., data characterized by a columnar organization. To this end, the DataFrame class provides, for instance, column labeling as well as flexible indexing capabilities for the rows (records) of the data set, similar to a table in a relational database or an Excel spreadsheet.

On a fundamental level, the `DataFrame` class is designed to manage indexed and labeled data, not too different from a SQL database table or a worksheet in a spreadsheet application. 

Consider the following creation of a DataFrame object:

```python

In [1]: import pandas as pd  1

In [2]: df = pd.DataFrame([10, 20, 30, 40],  2
                          columns=['numbers'],  3
                          index=['a', 'b', 'c', 'd'])  4

In [3]: df  5
Out[3]:    numbers
        a       10
        b       20
        c       30
        d       40

```

This simple example already shows some major features of the DataFrame class when it comes to storing data:
* Data itself can be provided in different shapes and types (list, tuple, ndarray, and dict objects are candidates).
* Data is organized in columns, which can have custom names (labels).
* There is an index that can take on different formats (e.g., numbers, strings, time information).


Working with a DataFrame object is in general pretty convenient and efficient with regard to the handling of the object, e.g., 
compared to regular ndarray objects, which are more specialized and more restricted when one wants to (say) enlarge an existing object. 
At the same time, DataFrame objects are often as computationally efficient as ndarray objects. 
The following are simple examples showing how typical operations on a DataFrame object work:

```python
In [4]: df.index  # The index attribute and Index object.
Out[4]: Index(['a', 'b', 'c', 'd'], dtype='object')

In [5]: df.columns  # The columns attribute and Index object.
Out[5]: Index(['numbers'], dtype='object')

In [6]: df.loc['c']  # Selects the value corresponding to index c
Out[6]: numbers    30
        Name: c, dtype: int64

In [7]: df.loc[['a', 'd']]  # Selects the two values corresponding to indices a and d.
Out[7]:    numbers
        a       10
        d       40

In [8]: df.iloc[1:3]  # Selects the second and third rows via the index positions.
Out[8]:    numbers
        b       20
        c       30

In [9]: df.sum()  # Calculates the sum of the single column.
Out[9]: numbers    100
        dtype: int64

In [10]: df.apply(lambda x: x ** 2)  # Uses the apply() method to calculate squares in vectorized fashion.
Out[10]:    numbers
         a      100
         b      400
         c      900
         d     1600

In [11]: df ** 2  # Applies vectorization directly as with ndarray objects.
Out[11]:    numbers
         a      100
         b      400
         c      900
         d     1600
```

Contrary to NumPy ndarray objects, enlarging the DataFrame object in both dimensions is possible:

```python
In [12]: df['floats'] = (1.5, 2.5, 3.5, 4.5)  # Adds a new column with float objects provided as a tuple object.

In [13]: df
Out[13]:    numbers  floats
         a       10     1.5
         b       20     2.5
         c       30     3.5
         d       40     4.5

In [14]: df['floats']  # Selects this column and shows its data and index labels.
Out[14]: a    1.5
         b    2.5
         c    3.5
         d    4.5
         Name: floats, dtype: float64
```

A whole DataFrame object can also be taken to define a new column. In such a case, indices are aligned automatically:

```python

In [15]: df['names'] = pd.DataFrame(['Yves', 'Sandra', 'Lilli', 'Henry'],
                                    index=['d', 'a', 'b', 'c'])  # Another new column is created based on a DataFrame object

In [16]: df
Out[16]:    numbers  floats   names
         a       10     1.5  Sandra
         b       20     2.5   Lilli
         c       30     3.5   Henry
         d       40     4.5    Yves

```

Appending data works similarly. However, in the following example a side effect is seen that is usually to be avoided—namely, the index gets replaced by a simple range index:

```python
In [17]: df.append({'numbers': 100, 'floats': 5.75, 'names': 'Jil'},
                        ignore_index=True)  # Appends a new row via a dict object; this is a temporary operation during which index information gets lost.
Out[17]:    numbers  floats   names
         0       10    1.50  Sandra
         1       20    2.50   Lilli
         2       30    3.50   Henry
         3       40    4.50    Yves
         4      100    5.75     Jil

In [18]: df = df.append(pd.DataFrame({'numbers': 100, 'floats': 5.75,
                                      'names': 'Jil'}, index=['y',]))  # Appends the row based on a DataFrame object with index information; the original index information is preserved.

In [19]: df
Out[19]:    numbers  floats   names
         a       10    1.50  Sandra
         b       20    2.50   Lilli
         c       30    3.50   Henry
         d       40    4.50    Yves
         y      100    5.75     Jil

In [20]: df = df.append(pd.DataFrame({'names': 'Liz'}, index=['z',]),
                        sort=False)  # Appends an incomplete data row to the DataFrame object, resulting in NaN values.

In [21]: df
Out[21]:    numbers  floats   names
         a     10.0    1.50  Sandra
         b     20.0    2.50   Lilli
         c     30.0    3.50   Henry
         d     40.0    4.50    Yves
         y    100.0    5.75     Jil
         z      NaN     NaN     Liz

In [22]: df.dtypes  # Returns the different dtypes of the single columns; this is similar to what’s possible with structured ndarray objects.
Out[22]: numbers    float64
         floats     float64
         names       object
         dtype: object

```

Although there are now missing values, the majority of method calls will still work:

```python
In [23]: df[['numbers', 'floats']].mean()  # Calculates the mean over the two columns specified (ignoring rows with NaN values).
Out[23]: numbers    40.00
         floats      3.55
         dtype: float64

In [24]: df[['numbers', 'floats']].std()  # Calculates the standard deviation over the two columns specified (ignoring rows with NaN values).
Out[24]: numbers    35.355339
         floats      1.662077
         dtype: float64

```

The example in this subsection is based on an ndarray object with standard normally distributed random numbers. It explores further features such as a DatetimeIndex to manage time series data:

```python
In [25]: import numpy as np

In [26]: np.random.seed(100)

In [27]: a = np.random.standard_normal((9, 4))

In [28]: a
Out[28]: array([[-1.74976547,  0.3426804 ,  1.1530358 , -0.25243604],
                [ 0.98132079,  0.51421884,  0.22117967, -1.07004333],
                [-0.18949583,  0.25500144, -0.45802699,  0.43516349],
                [-0.58359505,  0.81684707,  0.67272081, -0.10441114],
                [-0.53128038,  1.02973269, -0.43813562, -1.11831825],
                [ 1.61898166,  1.54160517, -0.25187914, -0.84243574],
                [ 0.18451869,  0.9370822 ,  0.73100034,  1.36155613],
                [-0.32623806,  0.05567601,  0.22239961, -1.443217  ],
                [-0.75635231,  0.81645401,  0.75044476, -0.45594693]])

```

Although one can construct DataFrame objects more directly (as seen before), using an ndarray object is generally a good choice since pandas will retain the basic structure and will “only” add metainformation (e.g., index values). It also represents a typical use case for financial applications and scientific research in general. For example:

```python
In [29]: df = pd.DataFrame(a)  # Creates a DataFrame object from the ndarray object

In [30]: df
Out[30]:           0         1         2         3
         0 -1.749765  0.342680  1.153036 -0.252436
         1  0.981321  0.514219  0.221180 -1.070043
         2 -0.189496  0.255001 -0.458027  0.435163
         3 -0.583595  0.816847  0.672721 -0.104411
         4 -0.531280  1.029733 -0.438136 -1.118318
         5  1.618982  1.541605 -0.251879 -0.842436
         6  0.184519  0.937082  0.731000  1.361556
         7 -0.326238  0.055676  0.222400 -1.443217
         8 -0.756352  0.816454  0.750445 -0.455947

```

Following lists the parameters that the DataFrame() function takes. In the table, “array-like” means a data structure similar to an ndarray object—a list, for example. Index is an instance of the pandas Index class.
            Format                                 Description
`data`:    ndarray/dict/DataFrame       Data for DataFrame; dict can contain Series, ndarray, list
`index`:   Index/array-like             Index to use; defaults to range(n)
`columns`: Index/array-like             Column headers to use; defaults to range(n)
`dtype`:   dtype, default None          Data type to use/force; otherwise, it is inferred
`copy`:    bool, default None           Copy data from inputs

As with structured arrays, and as seen before, DataFrame objects have column names that can be defined directly by assigning a list object with the right number of elements. This illustrates that one can define/change the attributes of the DataFrame object easily:

```python
In [31]: df.columns = ['No1', 'No2', 'No3', 'No4']  # Specifies the column labels via a list object.

In [32]: df
Out[32]:         No1       No2       No3       No4
         0 -1.749765  0.342680  1.153036 -0.252436
         1  0.981321  0.514219  0.221180 -1.070043
         2 -0.189496  0.255001 -0.458027  0.435163
         3 -0.583595  0.816847  0.672721 -0.104411
         4 -0.531280  1.029733 -0.438136 -1.118318
         5  1.618982  1.541605 -0.251879 -0.842436
         6  0.184519  0.937082  0.731000  1.361556
         7 -0.326238  0.055676  0.222400 -1.443217
         8 -0.756352  0.816454  0.750445 -0.455947

In [33]: df['No2'].mean()  # Picking a column is now made easy.
Out[33]: 0.7010330941456459

```

To work with financial time series data efficiently, one must be able to handle time indices well. 
This can also be considered a major strength of pandas. For example, assume that our nine data entries in the four columns correspond to month-end data, beginning in January 2019. A DatetimeIndex object is then generated with the date_range() function as follows:

```python
In [34]: dates = pd.date_range('2019-1-1', periods=9, freq='M')  # Creates a DatetimeIndex object.

In [35]: dates
Out[35]: DatetimeIndex(['2019-01-31', '2019-02-28', '2019-03-31', '2019-04-30',
                        '2019-05-31', '2019-06-30', '2019-07-31', '2019-08-31',
                        '2019-09-30'],
                       dtype='datetime64[ns]', freq='M')

```

[index](/)
