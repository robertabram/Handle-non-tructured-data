# Handle-non-tructured-data

import pandas as pd
import numpy as np

df = pd.ExcelFile('reshaping_data.xlsx')
df.sheet_names
data = df.parse(sheetname='ABC_inc',skiprows=7).head(10)
cols1 = list(data.columns)
cols1 = [str(x)[:4] for x in cols1]
cols2 = list(data.iloc[0,:])
cols2 = [str(x) for x in cols2]
cols = [x+'_'+y for x,y in zip(cols1,cols2)]
data.columns = cols
data.head(1)

tabname = df.sheet_names
i = 0

data = data.drop(['Unna_nan'],axis=1).iloc[1:,:].rename(columns={'dist_nan':'district','prov_nan': 'province',
            'part_nan':'partner','fund_nan':'financing_source'})

data['Main Organization'] = tabname[i].split("_")[0]+' '+tabname[i].split("_")[1]

to_remove = [c for c in data.columns if 'Total' in c]
to_change = [c for c in data.columns if 'yrs' in c]

data.drop(to_remove, axis=1,inplace=True)

for c in to_change:
    data[c] = pd.to_numeric(data[c])

idx = ['district','province','partner','financing_source','Main Organization']
multi_data = data.set_index(idx)
multi_data
stack_data = multi_data.stack(dropna=False)
long_data = stack_data.reset_index()
cols_str = long_data.level_5.str.split('_')
long_data['Target Year'] = [x[0] for x in cols_str]
long_data['Target age'] = [x[1] for x in cols_str]
long_data['Target Quantity'] = long_data[0]
long_data.drop(columns=0,axis=1,inplace=True)
