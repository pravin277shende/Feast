# feast-store-local

## Let's get started with deploying a local feature store with a `Parquet file offline store` and `Sqlite online store`.

### Install Feast
`pip install feast==0.31.1`

###  Create a feature repository
`feast init my_project`

Output

`Creating a new Feast repository in /home/Jovyan/my_project.`

Now

`cd my_project/feature_repo`

### Move `inspect_data.py` , `get_data_inference.py` & `gen_train_data.py` inside `my_project/feature_repo`

### Run python script `inspect.py` to inspect your data

![spaces_bfkpmICjbK4wjNolbKV2_uploads_git-blob-b2961c773e43f78f59e993ebde00b25a3ff7aca7_screen-shot-2021-08-23-at-2 35 18-pm](https://user-images.githubusercontent.com/22353843/222173075-3932fb8d-1b7c-40cc-9c0c-561ac7d4a752.png)

### Run sample workflow

`feast apply`

Output 
```
Created entity driver
Created feature view driver_hourly_stats
Created on demand feature view transformed_conv_rate
Created feature service driver_activity_v1
Created feature service driver_activity_v2

Created sqlite table my_project_driver_hourly_stats
```

### Run python script `gen_train_data.py` to generate training data

Ouput

```
----- Feature schema -----

<class 'pandas.core.frame.DataFrame'>
Int64Index: 3 entries, 0 to 2
Data columns (total 6 columns):
 #   Column                              Non-Null Count  Dtype
---  ------                              --------------  -----
 0   event_timestamp                     3 non-null      datetime64[ns, UTC]
 1   driver_id                           3 non-null      int64
 2   label_driver_reported_satisfaction  3 non-null      int64
 3   conv_rate                           3 non-null      float32
 4   acc_rate                            3 non-null      float32
 5   avg_daily_trips                     3 non-null      int32
dtypes: datetime64[ns, UTC](1), float32(2), int32(1), int64(2)
memory usage: 132.0 bytes
None

----- Example features -----

                   event_timestamp  driver_id  ...  acc_rate  avg_daily_trips
0 2021-08-23 15:12:55.489091+00:00       1003  ...  0.077863              741
1 2021-08-23 15:49:55.489089+00:00       1002  ...  0.074327              113
2 2021-08-23 16:14:55.489075+00:00       1001  ...  0.105046              347

[3 rows x 6 columns]

```

### Ingest batch features into your online store by running 

```
CURRENT_TIME=$(date -u +"%Y-%m-%dT%H:%M:%S")
feast materialize-incremental $CURRENT_TIME
```
Output

```
Materializing 1 feature views to 2021-08-23 16:25:46+00:00 into the sqlite online
store.

driver_hourly_stats from 2021-08-22 16:25:47+00:00 to 2021-08-23 16:25:46+00:00:
100%|████████████████████████████████████████████| 5/5 [00:00<00:00, 592.05it/s]
```

### Fetching feature vectors for inference by running `get_data_inference.py`
Output 

```
{
 'acc_rate': [0.5732735991477966, 0.7828438878059387],
 'avg_daily_trips': [33, 984],
 'conv_rate': [0.15498852729797363, 0.6263588070869446],
 'driver_id': [1004, 1005]
}
```

### Browse your features with the Web UI 

`feast ui`

Output

```
INFO:     Started server process [66664]
08/17/2022 01:25:49 PM uvicorn.error INFO: Started server process [66664]
INFO:     Waiting for application startup.
08/17/2022 01:25:49 PM uvicorn.error INFO: Waiting for application startup.
INFO:     Application startup complete.
08/17/2022 01:25:49 PM uvicorn.error INFO: Application startup complete.
INFO:     Uvicorn running on http://0.0.0.0:8888 (Press CTRL+C to quit)
08/17/2022 01:25:49 PM uvicorn.error INFO: Uvicorn running on http://0.0.0.0:8888 (Press CTRL+C to quit)
```
![feast-ui](https://user-images.githubusercontent.com/22353843/222175774-02150d8e-16f4-42a1-98a1-50238798a416.jpg)

