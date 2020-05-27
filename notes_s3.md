### reading and writing csvs directly to/from s3 with pandas
 - pandas now uses s3fs
 - read files from bucket with credentials
 ~~~
 import pandas as pd
 from s3fs.core import S3FileSystem

 s3 = S3FileSystem(anon=False, profile="dt-rachio")
 df = pd.read_csv(s3.open('s3://bucket/prefix/file.csv'))
 ~~~

 - write dataframe to bucket with credentials
 ~~~
 with s3.open('s3://bucket/prefix/file.csv', 'w') as f:
     df.to_csv(f, **kwargs)
 ~~~
