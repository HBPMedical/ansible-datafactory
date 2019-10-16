# ansible-datafactory

[![AUEB](https://img.shields.io/badge/AUEB-RC-red.svg)](http://rc.aueb.gr/el/static/home) [![HBP-SP8](https://img.shields.io/badge/HBP-SP8-magenta.svg)](https://www.humanbrainproject.eu/en/follow-hbp/news/category/sp8-medical-informatics-platform/)

Ansible deployment script for DataFactory. Creates the folders and scripts needed to run the EHR DataFactory pipeline on a hospital server.

## Requirements

- ansible 2.8.2 or greater

## Requirements for hospital server

- ubuntu 16.04
- docker 18.09 or greater
- docker-compose 1.22.0 or greater

## Deployment and Configuration


Update the hospital server IP address in 

Set up ssh with public key 

```shell
ssh <ansible_user>@<hospital_server IP> mkdir -p .ssh 
cat .ssh/id_rsa.pub|ssh <ansible_user>@<hospital_server IP> 'cat >> .ssh/authorized_keys 
```

Update the `datafactory.yml` **var** section with the hospital name , hospital server's postgres container name, port, postgres user and password.  **Do not** change the datafactory folders. 

The scripts creates `datafactory` linux user with a default password.


For changing the default password run **whois** tool in hospital server terminal:

```shell
mkpasswd --method=sha-512
```

Give the new password and copy the hashed string and update **df_pwd** key in `datafactory.yml`.

- add role **docker** for docker-compose deployment if it hasn't been already installed.

- add role **ehr_setup** for initial DataFactory deployment.
**Caution!** The following creation script drop any pre-existing DataFactory database. Skip this step if there is no such need *i.e. when importing a second batch of hospital data.*

executing the deployment scripts

```shell
ansible-playbook --ask-become-pass datafactory.yml 
```

```shell
pip install -r requirements.txt --user
```


### Data Factory Folders


| Path                                     | Description                              |
| ---------------------------------------- | ---------------------------------------- |
| /opt/DataFactory                         | DataFactory main config folder           |
| /opt/DataFactory/dbproperties            | DataFactory db properties folder         |
| /data/DataFactory/EHR/input              | DataFactory EHR data input folder        |
| /data/DataFactory/MRI/dicom/raw          | DataFactory DICOM raw data input folder  |
| /data/DataFactory/MRI/nifti/raw          | DataFactory NIFTI raw data input folder  |
| /data/DataFactory/output/                | DataFactory output folder                |
| /data/DataFactory/anonymized_output/     | DataFactory anonymized output folder     |
| /opt/DataFactory/preprocess_step         | DataFactory preprocess config folder     |
| /opt/DataFactory/capture_step            | DataFactory capture step config folder   |
| /opt/DataFactory/harmonize_step          | DataFactory harmonize tep config folder  |
| /opt/DataFactory/export_step             | DataFactory export sql scripts folder    |


### Data Factory enviroment variables

| Variable name            | Description                                    |
| ------------------------ | ---------------------------------------------- |
| DF_PATH                  | path of DataFactory scripts folder             |
| DF_DATA_PATH             | path of DataFactory DATA folder                |
