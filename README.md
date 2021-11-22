# Data warehousing & Reporting
## Best Practices

---

Pre-step

Install Cloud SDK fro `GCP` which contains `gcloud`, `gsutil` and `bq` command-line tools following official instructions [here](https://cloud.google.com/sdk/docs/install)

### IMPORTANT make sure that the account you are setting up has owner permissions in the project

---

## Please notice that you need to replace BUCKET_NAME with an unique name

<br>

### Upload a file to your bucket (S3/GCS)

1. Download [this](https://drive.google.com/file/d/1tSnINCBDWziorV9PO67M6eYbl7maOlXs/view?usp=sharing) file
2. Upload it to your bucket
- For GCS use: </br>

```python
# maybe you need to reauthenticate
gcloud auth login --update-adc

# validate project info with
gcloud config list

# if is not the project you want use
gcloud config set project {PROJECT_ID}

# to create a bucket
gsutil mb gs://BUCKET_NAME

# to upload into a bucket use
# gsutil cp origin destination
gsutil cp segments.csv gs://BUCKET_NAME
```

---
### Copy a file from a bucket to your bucket (GCS)

- For GCP use: </br>

```python
# to copy from a bucket use
# gsutil cp origin destination
gsutil cp gs://de-bootcamp-2021/all_data.csv gs://BUCKET_NAME
```
---

### Prepare your data warehouse for ingestion

- For GCP create a `dataset`
```python
bq --location=US mk -d \
--description "This is my test dataset." \
mydataset
```
---
### Copy data from a bucket to a data warehouse

- For GCP use
```python
bq load \
--autodetect \
--source_format=CSV \
mydataset.mytable \
gs://BUCKET_NAME/all_data.csv

# if schema is not right maybe explicitly declare it
bq load \
--skip_leading_rows=1 \
--source_format=CSV \
mydataset.mytable \
gs://BUCKET_NAME/all_data.csv \
producto:STRING,presentacion:STRING,marca:STRING,categoria:STRING,catalogo:STRING,precio:STRING,fechaRegistro:STRING,cadenaComercial:STRING,giro:STRING,nombreComercial:STRING,direccion:STRING,estado:STRING,municipio:STRING,latitud:STRING,longitud:STRING

# if there is a schema mismatch error try deleting the current table
bq rm -t mydataset.mytable 

# If data was ingested without errors go to BigQUery UI and play around with SQL queries on your table
```
---

### Finally clean up

1. Delete files in the buckets

- For GCP use
```python
# you can remove specific object 
gsutil rm -r gs://BUCKET_NAME/all_data.csv
# or recursively delete
gsutil rm -r gs://BUCKET_NAME
```
2. Delete buckets

- For GCP use
```python
gsutil rb gs://BUCKET_NAME
```
3. Delete datawarehouse tables
 ```
 - For GCP use
```python
# remove the table
bq rm -t mydataset.mytable 

# remove the dataset
bq rm -d mydataset

# if you want to remove dataset and all its tables
bq rm -r -d mydataset
```
