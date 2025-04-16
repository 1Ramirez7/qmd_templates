---
title: "Intermediate Macroecomomics"
subtitle: "" # left blank
author: "Name here"
date: "March 10, 2025"
format:
  html:
    self-contained: true
    keep-md: true
    page-layout: full # article default custom
    theme:
      light: [flatly] # more themes can be found (https://quarto.org/docs/output-formats/html-themes.html)
      dark: [darkly]
    fontsize: 0.95em # changes all fonts. default is 1
    linestretch: 1.5 # distance btw lines. defualt is 1.5
    math: katex
    toc: true # switch between: false or true
    code-fold: true
    code-summary: "Show the code"
    code-overflow: wrap
    code-copy: hover
    code-tools:
        source: true
        toggle: true
        caption: See code
execute:
  python: .venv/Scripts/python.exe # important
  freeze: auto # or true, false
  cache: true
  cache-dir: .cache
editor:
  markdown:
    wrap: sentence
---




# 05CH 1 Solow simulations
I will use 06CH solow model to make this one since it is similar

The growth rate of output $\frac{\Delta A_{t+1}}{A_{t}}=\bar{z} L_{a t}=\bar{z} \bar{\ell} \bar{L}$ suggest all of this three parameters would increase the overall growth rate. $\ell$ is the fraction of the workforce creating ideas. $\bar{z}$ is how efficiently researches can use old ideas to create new valuable ideas. $\bar{L}$ is the size of the overall workforce. Any increase in any of this three parameters would cause an increase in the overall growth rate of an economy. 


__Model 1__

::: {#97ac0cf8 .cell execution_count=2}
``` {.python .cell-code}
# model 0.6
# adding rest of calculations

import matplotlib.pyplot as plt
import seaborn as sns
import numpy as np
import statsmodels.api as sm

import pandas as pd

parameters = {
    'Simulation Period (yrs)': 15,
    's': 0.2,
    'delta': 0.15,
    'alpha': 1/3,
    'A': 1, 
    'L': 1, 
    'k_star_initial': None,  # Initialize with None
}

# Extract values from the parameters to calculate for k_star_initial
s = parameters['s']
delta = parameters['delta']
alpha = parameters['alpha']
A = parameters['A']
L = parameters['L']

# Calculate k_star_initial and update parameters
k_star_initial = (s * A / delta) ** (1/(1-alpha))
parameters['k_star_initial'] = k_star_initial


quantitative_experiments = {
    'A': {'new_value': 2, 'time_period': 10, 'length': 91}
} # to add just use the same format & change parameter


# Initialize the DataFrame
num_periods = parameters['Simulation Period (yrs)'] + 1
df = pd.DataFrame({
    'Period': range(num_periods),
    's': [parameters['s']] * num_periods,
    'delta': [parameters['delta']] * num_periods,
    'alpha': [parameters['alpha']] * num_periods,
    'A': [parameters['A']] * num_periods,
    'L': [parameters['L']] * num_periods,
    'Delta k': [0.0] * num_periods, # will be calculated
    'k*': [parameters['k_star_initial']] * num_periods,
    'C': [0.0] * num_periods, # will be calculated
    'K': [0.0] * num_periods, # will be calculated
    'Y': [0.0] * num_periods, # will be calculated 
})

# Apply Quantitative Experiments
for param, changes in quantitative_experiments.items():
    for period in range(changes['time_period'], changes['time_period'] + changes['length']):
        if period < num_periods:
            df.at[period, param] = changes['new_value']

# calculate delta k
def calculate_Delta_k(df, row_index):
    if row_index == 0:
        return 0.0
    else:
        delta_k = df.at[row_index, 's'] * ((df.at[row_index, 'A'] * df.at[row_index - 1, 'k*']) ** (df.at[row_index, 'alpha'])) - df.at[row_index, 'delta'] * df.at[row_index - 1, 'k*']
        return delta_k # updated for 05ch

# calculate k* (def python functions can have *)
def calculate_k_star(df, row_index, k_star_initial):
    if row_index == 0:
        k_star_value = k_star_initial
    else:
        k_star_value = (df.at[row_index - 1, 'k*'] + df.at[row_index, 'Delta k'])
    return k_star_value # def doesn't change from 05 to 06

# Calculating 'C'
def calculate_C(df, row_index):
    C = (1 - df.at[row_index, 's']) * df.at[row_index, 'A'] * df.at[row_index, 'k*'] ** df.at[row_index, 'alpha']
    return C

# calculating 'K'
def calculate_K(df, row_index):
    K = df.at[row_index, 'k*'] * df.at[row_index, 'L']
    return K

# calculating 'Y' K needs to be calculated first for Y to work.
def calculate_Y(df, row_index):
    Y =  df.at[row_index, 'A'] * ((df.at[row_index, 'K'] ** df.at[row_index, 'alpha']) * (df.at[row_index, 'L'] ** (1 - df.at[row_index, 'alpha'])))
    return Y

# Loop def functions through DataFrame
for i in range(len(df)):
    df.at[i, 'Delta k'] = calculate_Delta_k(df, i)
    df.at[i, 'k*'] = calculate_k_star(df, i, k_star_initial)
    df.at[i, 'C'] = calculate_C(df, i)
    df.at[i, 'K'] = calculate_K(df, i)
    df.at[i, 'Y'] = calculate_Y(df, i)

df
```

::: {.cell-output .cell-output-display execution_count=1}

```{=html}
<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Period</th>
      <th>s</th>
      <th>delta</th>
      <th>alpha</th>
      <th>A</th>
      <th>L</th>
      <th>Delta k</th>
      <th>k*</th>
      <th>C</th>
      <th>K</th>
      <th>Y</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.333333</td>
      <td>1</td>
      <td>1</td>
      <td>0.000000</td>
      <td>1.539601</td>
      <td>0.923760</td>
      <td>1.539601</td>
      <td>1.154701</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.333333</td>
      <td>1</td>
      <td>1</td>
      <td>0.000000</td>
      <td>1.539601</td>
      <td>0.923760</td>
      <td>1.539601</td>
      <td>1.154701</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.333333</td>
      <td>1</td>
      <td>1</td>
      <td>0.000000</td>
      <td>1.539601</td>
      <td>0.923760</td>
      <td>1.539601</td>
      <td>1.154701</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.333333</td>
      <td>1</td>
      <td>1</td>
      <td>0.000000</td>
      <td>1.539601</td>
      <td>0.923760</td>
      <td>1.539601</td>
      <td>1.154701</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.333333</td>
      <td>1</td>
      <td>1</td>
      <td>0.000000</td>
      <td>1.539601</td>
      <td>0.923760</td>
      <td>1.539601</td>
      <td>1.154701</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.333333</td>
      <td>1</td>
      <td>1</td>
      <td>0.000000</td>
      <td>1.539601</td>
      <td>0.923760</td>
      <td>1.539601</td>
      <td>1.154701</td>
    </tr>
    <tr>
      <th>6</th>
      <td>6</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.333333</td>
      <td>1</td>
      <td>1</td>
      <td>0.000000</td>
      <td>1.539601</td>
      <td>0.923760</td>
      <td>1.539601</td>
      <td>1.154701</td>
    </tr>
    <tr>
      <th>7</th>
      <td>7</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.333333</td>
      <td>1</td>
      <td>1</td>
      <td>0.000000</td>
      <td>1.539601</td>
      <td>0.923760</td>
      <td>1.539601</td>
      <td>1.154701</td>
    </tr>
    <tr>
      <th>8</th>
      <td>8</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.333333</td>
      <td>1</td>
      <td>1</td>
      <td>0.000000</td>
      <td>1.539601</td>
      <td>0.923760</td>
      <td>1.539601</td>
      <td>1.154701</td>
    </tr>
    <tr>
      <th>9</th>
      <td>9</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.333333</td>
      <td>1</td>
      <td>1</td>
      <td>0.000000</td>
      <td>1.539601</td>
      <td>0.923760</td>
      <td>1.539601</td>
      <td>1.154701</td>
    </tr>
    <tr>
      <th>10</th>
      <td>10</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.333333</td>
      <td>2</td>
      <td>1</td>
      <td>0.060026</td>
      <td>1.599627</td>
      <td>1.871226</td>
      <td>1.599627</td>
      <td>2.339032</td>
    </tr>
    <tr>
      <th>11</th>
      <td>11</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.333333</td>
      <td>2</td>
      <td>1</td>
      <td>0.054756</td>
      <td>1.654382</td>
      <td>1.892338</td>
      <td>1.654382</td>
      <td>2.365422</td>
    </tr>
    <tr>
      <th>12</th>
      <td>12</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.333333</td>
      <td>2</td>
      <td>1</td>
      <td>0.049867</td>
      <td>1.704250</td>
      <td>1.911163</td>
      <td>1.704250</td>
      <td>2.388954</td>
    </tr>
    <tr>
      <th>13</th>
      <td>13</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.333333</td>
      <td>2</td>
      <td>1</td>
      <td>0.045352</td>
      <td>1.749601</td>
      <td>1.927967</td>
      <td>1.749601</td>
      <td>2.409959</td>
    </tr>
    <tr>
      <th>14</th>
      <td>14</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.333333</td>
      <td>2</td>
      <td>1</td>
      <td>0.041196</td>
      <td>1.790797</td>
      <td>1.942982</td>
      <td>1.790797</td>
      <td>2.428727</td>
    </tr>
    <tr>
      <th>15</th>
      <td>15</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.333333</td>
      <td>2</td>
      <td>1</td>
      <td>0.037381</td>
      <td>1.828178</td>
      <td>1.956408</td>
      <td>1.828178</td>
      <td>2.445510</td>
    </tr>
  </tbody>
</table>
</div>
```

:::
:::


::: {#eb014b1b .cell execution_count=3}
``` {.python .cell-code}
# code to calculate individual calculations. 

import pandas as pd

parameters = {
    'Simulation Period (yrs)': 15,
    's': 0.2,
    'delta': 0.15,
    'alpha': 1/3,
    'A': 1, 
    'L': 1, 
    'k_star_initial': None,  # Initialize with None
}

# Extract values from the parameters to calculate for k_star_initial
s = parameters['s']
delta = parameters['delta']
alpha = parameters['alpha']
A = parameters['A']
L = parameters['L']
k_star_initial = (s * A / delta) ** (1/(1-alpha))
parameters['k_star_initial'] = k_star_initial
K = 1.539601
# Calculate k_star_initial and update parameters
Y =  A * ((K ** alpha) * (L ** (1 - alpha)))

def calculate_Y(df, row_index):
    Y =  df.at[row_index, 'A'] * ((df.at[row_index, 'K'] ** df.at[row_index, 'alpha']) * (df.at[row_index, 'L'] ** (1 - df.at[row_index, 'alpha'])))
    return Y
Y
```

::: {.cell-output .cell-output-display execution_count=2}
```
1.1547006089194967
```
:::
:::


::: {#d53ddc67 .cell execution_count=4}
``` {.python .cell-code}
# calculating formulas

alpha = (1/3)
alpha
```

::: {.cell-output .cell-output-display execution_count=3}
```
0.3333333333333333
```
:::
:::


# spacer

# Model 2

__adding data frame excel file__

::: {#f1f80100 .cell execution_count=5}
``` {.python .cell-code}
# model 2
# adding data frame excel file
import pandas as pd

parameters = {
    'Simulation Period (yrs)': 200,
    's': 0.2,
    'delta': 0.15,
    'alpha': 1/3,
    'A': 1, 
    'L': 1, 
    'k_star_initial': None,  # Initialize with None
}

# Extract values from the parameters to calculate for k_star_initial
s = parameters['s']
delta = parameters['delta']
alpha = parameters['alpha']
A = parameters['A']
L = parameters['L']

# Calculate k_star_initial and update parameters
k_star_initial = (s * A / delta) ** (1/(1-alpha))
parameters['k_star_initial'] = k_star_initial


quantitative_experiments = {
    'A': {'new_value': 2, 'time_period': 10, 'length': 91}
} # to add just use the same format & change parameter


# Initialize the DataFrame
num_periods = parameters['Simulation Period (yrs)'] + 1
df = pd.DataFrame({
    'Period': range(num_periods),
    's': [parameters['s']] * num_periods,
    'delta': [parameters['delta']] * num_periods,
    'alpha': [parameters['alpha']] * num_periods,
    'A': [parameters['A']] * num_periods,
    'L': [parameters['L']] * num_periods,
    'Delta k': [0.0] * num_periods, # will be calculated
    'k*': [parameters['k_star_initial']] * num_periods,
    'C': [0.0] * num_periods, # will be calculated
    'K': [0.0] * num_periods, # will be calculated
    'Y': [0.0] * num_periods, # will be calculated 
})

# Apply Quantitative Experiments
for param, changes in quantitative_experiments.items():
    for period in range(changes['time_period'], changes['time_period'] + changes['length']):
        if period < num_periods:
            df.at[period, param] = changes['new_value']

# calculate delta k
def calculate_Delta_k(df, row_index):
    if row_index == 0:
        return 0.0
    else:
        delta_k = df.at[row_index, 's'] * ((df.at[row_index, 'A'] * df.at[row_index - 1, 'k*']) ** (df.at[row_index, 'alpha'])) - df.at[row_index, 'delta'] * df.at[row_index - 1, 'k*']
        return delta_k # updated for 05ch

# calculate k* (def python functions can have *)
def calculate_k_star(df, row_index, k_star_initial):
    if row_index == 0:
        k_star_value = k_star_initial
    else:
        k_star_value = (df.at[row_index - 1, 'k*'] + df.at[row_index, 'Delta k'])
    return k_star_value # def doesn't change from 05 to 06

# Calculating 'C'
def calculate_C(df, row_index):
    C = (1 - df.at[row_index, 's']) * df.at[row_index, 'A'] * df.at[row_index, 'k*'] ** df.at[row_index, 'alpha']
    return C

# calculating 'K'
def calculate_K(df, row_index):
    K = df.at[row_index, 'k*'] * df.at[row_index, 'L']
    return K

# calculating 'Y' K needs to be calculated first for Y to work.
def calculate_Y(df, row_index):
    Y =  df.at[row_index, 'A'] * ((df.at[row_index, 'K'] ** df.at[row_index, 'alpha']) * (df.at[row_index, 'L'] ** (1 - df.at[row_index, 'alpha'])))
    return Y

# Loop def functions through DataFrame
for i in range(len(df)):
    df.at[i, 'Delta k'] = calculate_Delta_k(df, i)
    df.at[i, 'k*'] = calculate_k_star(df, i, k_star_initial)
    df.at[i, 'C'] = calculate_C(df, i)
    df.at[i, 'K'] = calculate_K(df, i)
    df.at[i, 'Y'] = calculate_Y(df, i)



df
```

::: {.cell-output .cell-output-display execution_count=4}

```{=html}
<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Period</th>
      <th>s</th>
      <th>delta</th>
      <th>alpha</th>
      <th>A</th>
      <th>L</th>
      <th>Delta k</th>
      <th>k*</th>
      <th>C</th>
      <th>K</th>
      <th>Y</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.333333</td>
      <td>1</td>
      <td>1</td>
      <td>0.000000</td>
      <td>1.539601</td>
      <td>0.923760</td>
      <td>1.539601</td>
      <td>1.154701</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.333333</td>
      <td>1</td>
      <td>1</td>
      <td>0.000000</td>
      <td>1.539601</td>
      <td>0.923760</td>
      <td>1.539601</td>
      <td>1.154701</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.333333</td>
      <td>1</td>
      <td>1</td>
      <td>0.000000</td>
      <td>1.539601</td>
      <td>0.923760</td>
      <td>1.539601</td>
      <td>1.154701</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.333333</td>
      <td>1</td>
      <td>1</td>
      <td>0.000000</td>
      <td>1.539601</td>
      <td>0.923760</td>
      <td>1.539601</td>
      <td>1.154701</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.333333</td>
      <td>1</td>
      <td>1</td>
      <td>0.000000</td>
      <td>1.539601</td>
      <td>0.923760</td>
      <td>1.539601</td>
      <td>1.154701</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>196</th>
      <td>196</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.333333</td>
      <td>1</td>
      <td>1</td>
      <td>-0.000003</td>
      <td>1.539625</td>
      <td>0.923765</td>
      <td>1.539625</td>
      <td>1.154707</td>
    </tr>
    <tr>
      <th>197</th>
      <td>197</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.333333</td>
      <td>1</td>
      <td>1</td>
      <td>-0.000002</td>
      <td>1.539622</td>
      <td>0.923765</td>
      <td>1.539622</td>
      <td>1.154706</td>
    </tr>
    <tr>
      <th>198</th>
      <td>198</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.333333</td>
      <td>1</td>
      <td>1</td>
      <td>-0.000002</td>
      <td>1.539620</td>
      <td>0.923764</td>
      <td>1.539620</td>
      <td>1.154705</td>
    </tr>
    <tr>
      <th>199</th>
      <td>199</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.333333</td>
      <td>1</td>
      <td>1</td>
      <td>-0.000002</td>
      <td>1.539618</td>
      <td>0.923764</td>
      <td>1.539618</td>
      <td>1.154705</td>
    </tr>
    <tr>
      <th>200</th>
      <td>200</td>
      <td>0.2</td>
      <td>0.15</td>
      <td>0.333333</td>
      <td>1</td>
      <td>1</td>
      <td>-0.000002</td>
      <td>1.539617</td>
      <td>0.923764</td>
      <td>1.539617</td>
      <td>1.154704</td>
    </tr>
  </tbody>
</table>
<p>201 rows × 11 columns</p>
</div>
```

:::
:::


