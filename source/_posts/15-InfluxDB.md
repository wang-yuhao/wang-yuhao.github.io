---
title: 15-InfluxDB
date: 2020-06-29 08:52:06
tags:
---

## InfluxDB

#### Attention:
1. How to use DataFrame Client
```
client = DataFrameClient('localhost', 8086, user, password, dbname)
```

2. When we want to insert a DataFrame in InfluxDB, the DataFrame must have a time format index, for example:

```
# open test_data.json as record 
with open('data/test data/' + 'test_data.json') as record:
        # load json data as list
        new_record = json.load(record)
        # convert list to DataFrame, and get columns 'srcaddr','srcport','First'.
        new_record = pd.DataFrame(data=new_record,                       
                                  columns=['srcaddr','srcport','First'])
        # convert the format of "new_record['First']" to datetime
        new_record['First'] = pd.to_datetime(new_record['First'], format='%Y%m%d%H%M%S')
        # set column "First" as index
        new_record.set_index('First', inplace=True)
```
3. InfluxDB writes points with the same timestamp but different measured values, otherwise InfluxDB will only insert a newest timestamp record.

Solve:
    write_points use argument 'tags' or 'tag_columns'(recommand)

Example:
```
client.write_points(new_record, 'demo', tag_columns=['srcaddr'], protocol=protocol)
```

4. [InfluxDB API Documentation and Examples](https://influxdb-python.readthedocs.io/en/latest/api-documentation.html)

5. Complete example of InfluxDB using DataFrameClient.
```
"""Tutorial for using pandas and the InfluxDB client."""

import argparse
import pandas as pd
import json

from influxdb import DataFrameClient


def main():
    """Instantiate the connection to the InfluxDB client."""
    user = 'root'
    password = '0318'
    dbname = 'assetdb_ts'
    protocol = 'line'

    client = DataFrameClient('localhost', 8086, user, password, dbname)

    print("Create pandas DataFrame")
    with open('data/test data/' + 'test_data.json') as record:
        new_record = json.load(record)
        new_record = pd.DataFrame(data=new_record,                       
                                  columns=['srcaddr','srcport','First'])
        new_record['First'] = pd.to_datetime(new_record['First'], format='%Y%m%d%H%M%S')
        new_record.set_index('First', inplace=True)

        print("Create database: " + dbname)
        client.create_database(dbname)

        print("Write DataFrame")
        client.write_points(new_record, 'demo', tag_columns=['srcaddr'], protocol=protocol)

        print("Read DataFrame")
        result = client.query("select * from demo")
        print("Result: {0}".format(result))

        print("Delete database: " + dbname)
        client.drop_database(dbname)

if __name__ == '__main__':

    main()
```

[Original example:](https://influxdb-python.readthedocs.io/en/latest/examples.html#tutorials-pandas)
```
# -*- coding: utf-8 -*-
"""Tutorial for using pandas and the InfluxDB client."""

import argparse
import pandas as pd

from influxdb import DataFrameClient


def main(host='localhost', port=8086):
    """Instantiate the connection to the InfluxDB client."""
    user = 'root'
    password = 'root'
    dbname = 'demo'
    protocol = 'line'

    client = DataFrameClient(host, port, user, password, dbname)

    print("Create pandas DataFrame")
    df = pd.DataFrame(data=list(range(30)),
                      index=pd.date_range(start='2014-11-16',
                                          periods=30, freq='H'), columns=['0'])

    print("Create database: " + dbname)
    client.create_database(dbname)

    print("Write DataFrame")
    client.write_points(df, 'demo', protocol=protocol)

    print("Write DataFrame with Tags")
    client.write_points(df, 'demo',
                        {'k1': 'v1', 'k2': 'v2'}, protocol=protocol)

    print("Read DataFrame")
    client.query("select * from demo")

    print("Delete database: " + dbname)
    client.drop_database(dbname)


def parse_args():
    """Parse the args from main."""
    parser = argparse.ArgumentParser(
        description='example code to play with InfluxDB')
    parser.add_argument('--host', type=str, required=False,
                        default='localhost',
                        help='hostname of InfluxDB http API')
    parser.add_argument('--port', type=int, required=False, default=8086,
                        help='port of InfluxDB http API')
    return parser.parse_args()


if __name__ == '__main__':
    args = parse_args()
    main(host=args.host, port=args.port)
```

6. 'field' is the neccessary part of the influxDB data structure.
but 'tag' is not the neccessary part of the influxDB data structure, every 'tag' is a index, so the query of 'tag' is faster.

[More information about 'field' and 'tag'](https://jasper-zhang1.gitbooks.io/influxdb/content/Concepts/key_concepts.html)

7. How to add tags or tag_columns into influxDB DataFrameClient.
    tags:
```
tags = { "srcaddr": new_record[["srcaddr"]],  "srcport": new_record[["srcport"]]}
client.write_points(new_record, 'demo', tags = tags, protocol="json")
```

    tag_columns:
```
client.write_points(new_record, 'demo', tag_columns=['srcaddr','srcport'], protocol=protocol)
```

## Reference
[1. Influxdb-python](https://github.com/influxdata/influxdb-python)
[2. Helpful tag_columns answer](https://github.com/influxdata/influxdb-python/issues/576)