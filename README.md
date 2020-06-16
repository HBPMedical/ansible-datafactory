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


1. Update the hospital server IP address and the ansible user name in `development.txt`

2. Set up ssh with public key 

```shell
ssh <ansible_user>@<hospital_server IP> 'mkdir -p .ssh' 
cat .ssh/id_rsa.pub|ssh <ansible_user>@<hospital_server IP> 'cat >> .ssh/authorized_keys' 
```

3. Update the variables in `ehr_setup/vars/main.yml` file with the hospital name , hospital server's postgres container name, port, postgres user and password.  **Do not** change the datafactory folders.

The scripts creates `datafactory` linux user but in the ansible script this password must be encrypted in sha512. 

4. Run

```shell
mkpasswd --method=sha-512
```

5. Give the new password and copy the hashed string and update **df_pwd** key in `ehr_setup/vars/main.yml`.

6. Update the postgres docker container options in ``ehr_setup/vars/main.yml``

**Caution!** The following creation script drop any pre-existing DataFactory database. Skip this step if there is no such need *i.e. when importing a second batch of hospital data.*

7. If we want to install [LORIS-for-MIP](https://github.com/HBPMedical/LORIS-for-MIP.git) component, add the `loris_setup` role into the `datafactory.yml` playbook. Please edit the `loris_setup/vars/main.yml` for changing the default installation parameters.

8. Execute the deployment scripts

```shell
ansible-playbook --ask-become-pass datafactory.yml
```

Please check the documentation in [DataFactory github repo](https://github.com/aueb-wim/ehr-datafactory-template)


### Data Factory Folders

| Path                                             | Description                                   |
| ------------------------------------------------ | --------------------------------------------- |
| /opt/DataFactory                                 | DataFactory main folder                       |
| /opt/DataFactory/dbproperties                    | DataFactory db properties folder              |
| /data/DataFactory/EHR/input                      | DataFactory EHR data root input folder        |
| /data/DataFactory/MRI/dicom/raw                  | DataFactory DICOM raw data root input folder  |
| /data/DataFactory/MRI/nifti/raw                  | DataFactory NIFTI raw data root input folder  |
| /data/DataFactory/imaging                        | DataFactory imaging data root input folder    |
| /data/DataFactory/output/                        | DataFactory output root folder                |
| /data/DataFactory/anonymized_output/             | DataFactory anonymized output root folder     |
| /opt/DataFactory/mipmap_mappings/preprocess_step | DataFactory preprocess step config root folder|
| /opt/DataFactory/mipmap_mappings/capture_step    | DataFactory capture step config root folder   |
| /opt/DataFactory/mipmap_mappings/harmonize_step  | DataFactory harmonize step config root folder |
| /opt/DataFactory/mipmap_mappings/imaging_step    | DataFactory imaging mapping config folder     |
| /opt/DataFactory/export_step                     | DataFactory export sql scripts folder         |

### Data Factory enviroment variables

| Variable name            | Description                                    |
| ------------------------ | ---------------------------------------------- |
| DF_PATH                  | path of DataFactory scripts folder             |
| DF_DATA_PATH             | path of DataFactory DATA folder                |
