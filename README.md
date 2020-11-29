# Fugue

[![GitHub release](https://img.shields.io/github/release/fugue-project/fugue.svg)](https://GitHub.com/fugue-project/fugue)
[![PyPI pyversions](https://img.shields.io/pypi/pyversions/fugue.svg)](https://pypi.python.org/pypi/fugue/)
[![PyPI license](https://img.shields.io/pypi/l/fugue.svg)](https://pypi.python.org/pypi/fugue/)
[![PyPI version](https://badge.fury.io/py/fugue.svg)](https://pypi.python.org/pypi/fugue/)
[![Coverage Status](https://coveralls.io/repos/github/fugue-project/fugue/badge.svg)](https://coveralls.io/github/fugue-project/fugue)
[![Doc](https://readthedocs.org/projects/fugue/badge)](https://fugue.readthedocs.org)

[Join Fugue-Project on Slack](https://join.slack.com/t/fugue-project/shared_invite/zt-he6tcazr-OCkj2GEv~J9UYoZT3FPM4g)

Fugue is a pure abstraction layer that makes code portable across differing computing frameworks such as Pandas, Spark and Dask.

* :rocket: **No need to choose compute frameworks**: Write code once, and then port it over to Pandas, Dask or Spark. Logic is decoupled from frameworks, allowing users to apply the same code to Pandas, Dask, and Spark with minimal changes. The framework adapts to your code.
* :moneybag: **Rapid iterations for big data projects**: Test code on smaller data, then reliably scale to Spark when ready. This drastically improves project iteration time and saves cluster expense. Unit tests scale seamlessly from local workflows to distributed computing workflows.
* :wrench: **Optimizations for Spark**: Fugue handles some optimizations on Spark, making it easier for big data practitioners to focus on logic.

## Who is it for

* Big data practitioners looking to reduce compute costs and increase project velocity
* Data scientists transitioning from local workflows (Pandas) to distributed computing workflows (Dask or Spark)
* Data engineers scaling data pipelines to handle bigger data

## Key Features

Here is an example Fugue code snippet that illustrates some of the key features of the framework. A fillna function creates a new column named `filled`, which is the same as the column `val` except that the `None` values are filled.

```python
from typing import Iterable, Dict, Any, List

# Creating sample data
data = [
    ["A", "2020-01-01", 10],
    ["A", "2020-01-02", None],
    ["A", "2020-01-03", 30],
    ["B", "2020-01-01", 20],
    ["B", "2020-01-02", None],
    ["B", "2020-01-03", 40]
]
schema = "id:str,date:date,val:int"

# schema: *, filled:int
def fillna(df:Iterable[Dict[str,Any]],value:int=0) -> Iterable[Dict[str,Any]]:
    for row in df:
        for col in cols:
            row["filled"] = (row["val"] or value)
        yield row

with FugueWorkflow() as dag:
    df1 = dag.df(data, schema).transform(fillna)
    df1.show()
```

### Catch errors faster

Fugue builds a [directed acyclic graph (DAG)](https://fugue-tutorials.readthedocs.io/en/latest/tutorials/dag.html) before running code, allowing users to receive errors faster. This catches more errors before expensive jobs are run on a cluster. For example, mismatches in specified [schema](https://fugue-tutorials.readthedocs.io/en/latest/tutorials/schema_dataframes.html#Schema) will raise errors. In the code above, the schema hint comment is read and the schema is enforced during execution. Schema is required for Fugue [extensions](https://fugue-tutorials.readthedocs.io/en/latest/tutorials/extensions.html).

### Cross-platform execution

Notice that the `fillna` function written above is purely in native Python. The code will still run without Fugue. Fugue lets users write code in Python, and then port the logic to Pandas, Spark, or Dask. Users can focus on the logic, rather than on what engine it will be executed. To bring it to Spark, simply pass the `SparkExecutionEngine` into the `FugueWorkflow` as follows.

```python
from fugue_spark import SparkExecutionEngine

with FugueWorkflow(SparkExecutionEngine) as dag:
    df1 = dag.df(data, schema).transform(fillna)
    df1.show()
```

### Spark optimizations

Fugue makes Spark easier to use for people starting with distributed computing. For example, Fugue uses the constructed DAG to smartly [auto-persist](https://fugue-tutorials.readthedocs.io/en/latest/tutorials/useful_config.html#Auto-Persist) dataframes used multiple times.

### Access to underlying frameworks

Fugue does not restrict users from There is still access for Spark RDDs if they need to be accessed.

### [Fugue SQL](https://fugue-tutorials.readthedocs.io/en/latest/tutorials/sql.html)

A SQL-based language capable of expressing end-to-end workflows. It can also use functions defined in Python. The `fillna` code above is equivalent to the code below.

```python
with FugueSQLWorkflow() as dag:
    df1 = dag.df(data, schema)
    dag("""
    SELECT id, date, val, ifnull(val, 1) filled
    FROM df1
    PRINT
    """)
```

## Get started

To read the complete static docs, [click here](https://fugue.readthedocs.org)

The best way to start is to go through the tutorials. We have the tutorials in an interactive notebook environent.

### Run the tutorial using binder:
[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/fugue-project/tutorials/master)

**But it runs slow on binder**, the machine on binder isn't powerful enough for
a distributed framework such as Spark. Parallel executions can become sequential, so some of the
performance comparison examples will not give you the correct numbers.

### Run the tutorial using docker

Alternatively, you should get decent performance if running its docker image on your own machine:

```
docker run -p 8888:8888 fugueproject/tutorials:latest
```

## Installation
```
pip install fugue
```

Fugue has these extras:
* **sql**: to support [Fugue SQL](https://fugue-tutorials.readthedocs.io/en/latest/tutorials/sql.html)
* **spark**: to support Spark as the [ExecutionEngine](https://fugue-tutorials.readthedocs.io/en/latest/tutorials/execution_engine.html)
* **dask**: to support Dask as the [ExecutionEngine](https://fugue-tutorials.readthedocs.io/en/latest/tutorials/execution_engine.html)

For example a common use case is:
```
pip install fugue[sql,spark]
```

## Contributing

Feel free to message us on [Slack](https://join.slack.com/t/fugue-project/shared_invite/zt-jl0pcahu-KdlSOgi~fP50TZWmNxdWYQ). We also have [contributing instructions](CONTRIBUTING.md).
