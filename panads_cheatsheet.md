# Pandas Cheatsheet

## Core Concepts
- Series: 1D labeled array (like a column)
- DataFrame: 2D labeled table of rows and columns
- Index types: Index, RangeIndex, DatetimeIndex, CategoricalIndex
- Dtypes: object, string, category, int, float, bool, datetime64[ns][tz], timedelta64, period, interval

## Imports
- import pandas as pd
- import numpy as np

## Creating Objects
- pd.Series([1,2,3], name='s')
- pd.DataFrame({'a':[1,2],'b':[3,4]})
- pd.date_range('2024-01-01', periods=5, freq='D')
- pd.Categorical(['a','b','a'])

## I/O (Read/Write)
- pd.read_csv('file.csv'); df.to_csv('out.csv', index=False)
- pd.read_excel('file.xlsx', sheet_name=0); df.to_excel('out.xlsx', index=False)
- pd.read_parquet('file.parquet'); df.to_parquet('out.parquet')
- pd.read_json('file.json'); df.to_json('out.json', orient='records')
- pd.read_sql('SELECT * FROM t', con); df.to_sql('t', con, if_exists='replace')
- pd.read_html('https://example.com')
- pd.read_clipboard(); df.to_clipboard(index=False)

## Inspecting Data
- df.head(n) / df.tail(n)
- df.sample(n)
- df.info()
- df.describe(include='all')
- df.shape, df.columns, df.index, df.dtypes

## Selection
- df['col'], df[['a','b']]
- df.loc[row_labels, col_labels]
- df.iloc[row_positions, col_positions]
- df.at[label, 'col'] (scalar)
- df.query('a > 0 and b == "x"')

## Filtering and Boolean Logic
- df[df['col'] > 0]
- df[(df['a'] > 0) & (df['b'] == 'x')]
- df['col'].between(10, 20)
- df['col'].isin(['x','y'])

## Sorting
- df.sort_values(['col1','col2'], ascending=[True, False])
- df.sort_index()

## Missing Data
- df.isna(), df.notna()
- df.fillna(value)
- df.dropna(subset=['col'], how='any')
- df.interpolate()

## Type Conversion
- df.astype({'a':'Int64', 'b':'string'})
- pd.to_numeric(df['x'], errors='coerce')
- pd.to_datetime(df['date'], utc=True, errors='coerce')
- pd.to_timedelta(df['td'])

## String Operations (Series.str)
- s.str.lower(), s.str.upper(), s.str.strip()
- s.str.contains('pat', case=False, na=False)
- s.str.replace('x','y', regex=False)
- s.str.extract('(\d+)')
- s.str.split('-', expand=True)

## Datetime Operations (Series.dt)
- s.dt.year, s.dt.month, s.dt.day, s.dt.weekday
- s.dt.tz_localize('UTC').dt.tz_convert('US/Pacific')
- df.set_index('date').resample('M').sum()

## Arithmetic and Apply
- df.add, sub, mul, div, pow (aligns on index/columns)
- df.apply(func, axis=1)
- df.assign(newcol=lambda d: d['a'] + d['b'])
- np.where(cond, x, y) for vectorized conditionals

## Aggregation
- df.agg({'a':'sum', 'b':['mean','median']})
- df['a'].sum(), mean(), median(), min(), max(), nunique()
- value_counts(normalize=True, dropna=False)

## GroupBy
- df.groupby('key')['val'].sum()
- df.groupby(['k1','k2']).agg({'v':'mean', 'w':'sum'})
- g = df.groupby('k'); g.size(), g.ngroup(), g.rank()
- g.transform('sum') for broadcasting grouped stats

## Pivot / Reshape
- df.pivot(index='i', columns='j', values='v')
- df.pivot_table(index='i', columns='j', values='v', aggfunc='sum')
- df.melt(id_vars=['id'], var_name='variable', value_name='value')
- df.stack() / df.unstack()

## Merge / Join / Concatenate
- pd.merge(df1, df2, on='key', how='left')
- df1.join(df2, how='inner', lsuffix='_l', rsuffix='_r')
- pd.concat([df1, df2], axis=0, ignore_index=True)
- pd.concat([df1, df2], axis=1)

## Window Operations
- df['x'].rolling(7).mean()
- df['x'].expanding().sum()
- df['x'].ewm(alpha=0.2).mean()

## Categorical Data
- df['cat'] = pd.Categorical(df['cat'], categories=['low','med','high'], ordered=True)
- df['cat'].cat.codes

## Missing-Friendly dtypes
- 'Int64', 'Float64', 'boolean', 'string' support NA

## Options and Display
- pd.set_option('display.max_rows', 200)
- pd.option_context('display.float_format', '{:.2f}'.format)

## Performance Tips
- Prefer vectorized ops over .apply(axis=1)
- Use categorical dtype for low-cardinality text
- Use parquet/feather for faster I/O
- For very large data: chunked reading (chunksize=), PyArrow backend

## Plotting (quick)
- df.plot(kind='line'|'bar'|'hist'); import matplotlib.pyplot as plt; plt.show()

## Testing and Type Utilities
- pd.testing.assert_frame_equal(df1, df2)
- from pandas.api import types as ptypes; ptypes.is_numeric_dtype(df['a'])

## Common Patterns
- Chain: df.assign(y=lambda d: d['a']+d['b']).query('y>0').sort_values('y')
- Safe math: df['r'] = df['num'].div(df['den']).fillna(0)
- De-duplicate: df.drop_duplicates(subset=['k'], keep='last')
- Unique combos: df.value_counts(['a','b']).reset_index(name='n')
- Binning: pd.cut(df['x'], bins=[0,10,20], labels=['low','high'])
