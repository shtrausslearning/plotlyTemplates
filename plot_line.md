
```python
import pandas_datareader as pdr
import pandas as pd
import numpy as np
import plotly.express as px
import plotly.graph_objects as go
from plotly.subplots import make_subplots

def plot_line(ldf,                     # input dataframe
              lst,                     # plot features
              title=None,              # title of plot
              sec_id=None,             # if secondary_y axis is used
              size=[400,None],         # size of plot
              tight_layout=False,      # tight layout
              y_names = None,          # names of y-axis labels    
              interactive=False):      # interactive/static plot
    
    # sec_id - list of [False,False,True] values of 
    # when to activate supblots; same length as lst
    
    if(sec_id != None):
        fig = make_subplots(specs=[[{"secondary_y": True}]])
    else:
        fig = go.Figure()
        
    if(len(lst) != 1):
        for ii,i in enumerate(lst):
        
            if(sec_id != None):
                
                second_id = sec_id[ii]
                y_id = ldf[[i]]  
                
                fig.add_trace(go.Scatter(x=ldf.index.tolist(), 
                                         y=y_id[i].values,
                                         mode='lines',
                                         name=i,
                                         line=dict(width=2.0)),
                              secondary_y=second_id)
            else:
                fig.add_trace(go.Scatter(x=ldf.index, 
                                         y=y_id[i],
                                         mode='lines',
                                         name=i,
                                         line=dict(width=2.0)))
    else:
        fig.add_trace(go.Scatter(x=ldf.index, y=ldf[lst[0]],
                                 mode='lines',name=lst[0],
                                 line=dict(width=2.0)))

    if(tight_layout is True):
        fig.update_layout(height=size[0],width=size[1],
                          template='plotly_white',title=title,
                          margin=dict(l=50,r=80,t=50,b=40))
    else:
        fig.update_layout(height=size[0],width=size[1],
                          template='plotly_white',title=title)
        
    # Set y-axes titles
    if(sec_id != None and y_names != None):
        fig.update_yaxes(title_text = y_names[0], secondary_y=False)
        fig.update_yaxes(title_text = y_names[1], secondary_y=True)
    elif(sec_id != None and y_names == None):
        fig.update_yaxes(title_text = f'Y-Axis #1', secondary_y=False)
        fig.update_yaxes(title_text = f'Y-Axis #2', secondary_y=True)
        
        
    if(interactive is True):
        fig.show()
    else:
        fig.show(renderer="svg")
```

- Assemble some sample <code>time-series</code> data

```python
# Closing share price of APPLE and MICROSOFT
shares_aapl = pdr.DataReader('AAPL', 'yahoo', start='2021-01-01', end='2021-12-31')['Close']
shares_msft = pdr.DataReader('MSFT', 'yahoo', start='2021-01-01', end='2021-12-31')['Close']

shares_df = pd.concat([shares_aapl,shares_msft],axis=1)
shares_df.columns = ['AAPL','MSFT']
shares_df.head()
```

```python

# Closing share price of APPLE and MICROSOFT
shares_aapl = pdr.DataReader('AAPL', 'yahoo', start='2021-01-01', end='2021-12-31')['Close']
shares_msft = pdr.DataReader('MSFT', 'yahoo', start='2021-01-01', end='2021-12-31')['Close']*0.0001

# create dataset
shares_df = pd.concat([shares_aapl,shares_msft],axis=1)
shares_df.columns = ['AAPL','MSFT']
shares_df.head()

- Plot <code>AAPL</code> on y-axis #1 & <code>MSFT</code> on y-axis #2

plot_line(shares_df,['AAPL','MSFT'],
          title='Closing Share Price of Microsoft & Apple',
          interactive=True,
          sec_id=[False,True],
          tight_layout=False)

```
