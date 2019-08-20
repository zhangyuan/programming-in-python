# Data Analytics with Pandas

## Pandas

```
pip install pandas sqlalchemy sqlite3
```

```python
import pandas as pd
from sqlalchemy import create_engine

df = pd.read_csv('./orders.csv')

engine = create_engine('sqlite:///df.db', echo=True)

df.to_sql(engine)
```

## Jupyter

```
pip install jupyterlab
```

```
jyputer notebook
```