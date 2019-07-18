# ansible-datafactory

[![AUEB](https://img.shields.io/badge/AUEB-RC-red.svg)](http://rc.aueb.gr/el/static/home) [![HBP-SP8](https://img.shields.io/badge/HBP-SP8-magenta.svg)](https://www.humanbrainproject.eu/en/follow-hbp/news/category/sp8-medical-informatics-platform/)

Ansible deployment script for DataFactory. Creates the folders and scripts needed to run the EHR DataFactory pipeline on a hospital server.

## Requirements

- ubuntu 16.04
- docker 18.09 or greater
- docker-compose 1.22.0 or greater

## Deployment and Configuration

Update the `datafactory.yml` **var** section with the hospital server's postgres container name, port, postgres user and password. The scripts creates `datafactory`
linux user. For changing the default password use **whois** tool and run in terminal:
```shell
mkpasswd --method=sha-512 
```
Give the new password and copy the hashed string and update **df_pwd** key in `datafactory.yml`.

- add role **docker** for docker-compose deployment if it hasn't been allready installed.

- add role **ehr_setup** for initial DataFactory deployment.
**Caution!** The following creation script drop any prexisting DataFactory database. Skip this step if there is no such need *i.e. when importing a second batch of hospital data.*

### Upload mapping config files

#### Preprocess step

- add role **ehr_upload_preprocess**
- copy config files into the folder `roles/ehr_upload_preprocess/files/preprocess_step`

#### Capture step

- add role **ehr_upload_capture**
- copy config files into the folder `roles/ehr_upload_capture/files/capture_step`


#### Harmonize step

- add role **ehr_upload_harmonize**
- copy config files into the folder `roles/ehr_upload_harmonize/files/harmonize_step`


### Data Factory Folders

| Path                                     | Description                              |
| ---------------------------------------- | ---------------------------------------- |
| /opt/DataFactory                         | DataFactory main config folder           |
| /opt/DataFactory/dbproperties            | DataFactory db properties folder         |
| /opt/DataFactory/dbsetup                 | DataFactory db setup folder              |
| /data/DataFactory/input/EHR              | DataFactory EHR data input folder        |
| /data/DataFactory/input/DICOM            | DataFactory DICOM data input folder      |
| /data/DataFactory/output/                | DataFactory output folder                |
| /data/DataFactory/anonymized_output/     | DataFactory anonymized output folder     |
| /opt/DataFactory/preprocess_step         | DataFactory preprocess config folder     |
| /opt/DataFactory/capture_step            | DataFactory capture step config folder   |
| /opt/DataFactory/harmonize_step          | DataFactory harmonize tep config folder  |
| /opt/DataFactory/export_step             | DataFactory export sql scripts folder    |

### Data Factory enviroment variables

| Variable name            | Description                                    |
| ------------------------ | ---------------------------------------------- |
| DF_DB_PROPERTIES         | path of DataFactory db properties folder       |
| DF_OUTPUT_PATH           | path of DataFactory output folder              |
| DF_ANON_OUTPUT_PATH      | path of DataFactory anonymized output folder   |
| DF_SOURCE_PATH           | path of DataFactory EHR input folder           |
| MIPMAP_PREPROCESS        | path of DataFactory preprocess config folder   |
| MIPMAP_CAPTURE           | path of DataFactory capture config folder      |
| MIPMAP_HARMONIZE         | path of DataFactory harmonize config folder    |
| MIPMAP_EXPORT            | path of DataFactory export sql scripts folder  |


## Running EHR DataFactory pipeline

### Step_1 - preprocess step

In /opt/DataFactory folder run

```shell
sh ingestdata.sh preprocess
```

Auxiliary files are created in the same folder where the hospital csv files are located.

### Step_2 - capture step

In /opt/DataFactory folder run

```shell
sh ingestdata.sh capture
```

### Step_3 - harmonization step

In /opt/DataFactory folder run

```shell
sh ingestdata.sh harmonize
```

### Step_4 - local data flattening step

In /opt/DataFactory folder run

```shell
sh ingestdata.sh export
```

harmonized_clinical_data.csv is created in the mipmap output folder 

### Step_5 - Anonymization step

In /opt/DataFactory folder run

```shell
sh anonymize.sh i2b2
```

### Step_6 - anonymized data flattening step

In /opt/DataFactory folder run

```shell
sh anonymize.sh export
```

harmonized_clinical_anon_data.csv is created in the mipmap anonym_output folder
