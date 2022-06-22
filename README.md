# The ECHOES EO Processing Service

This is the high-level documentation for the ECHOES Project code.  

To view the documentation in a browser install [docsify](https://docsify.js.org/), cd to the current directory, 
and enter the command: 
    
    docsify serve


## Introduction

The ECHOES Earth Observation (EO) code is used to provide an EO service for the ECHOES web application. The EO service
is decoupled for the web service and can be used independently of it

The processing chains have been developed to run in the cloud. Advantages of using cloud services, such as Creodias and
AWS, include:

* they can take advantage of the satellite data available there
* the size of the VMs can be increased or decreased as required

## The EO Processing Packages

There are two Python packages for EO processing with
ECHOES: [eo-custom-scripts](https://github.com/ECHOESProj/eo-custom-scripts)
and [eoian](https://github.com/ECHOESProj/eoian]). [eo-custom-scripts](https://github.com/ECHOESProj/eo-custom-scripts)
uses the Sentinel-Hub API to generate products, whereas [eoian](https://github.com/ECHOESProj/eoian]) consumes satellite
data stored in the object store (e.g. in the SAFE format for Sentinel-2 data).

With eo-custom-scripts, the processing is done on Sentinel-Hub's servers, whereas with eoian the processing is done
locally. Therefore, the machine requirements may be greater for the eoian processing chain, depending on the processing.

eoian and eo-custom-scripts can each be called by a CLI interface or imported as a Python module. See the README of
these packages for information on their installation and usage.

## eo-custom-scripts

[Sentinel Hub](https://www.sentinel-hub.com/) is a multi-spectral and multi-temporal big data satellite imagery service.
Users can use APIs to retrieve satellite data over their AOI and specific time range from full archives in a matter of
seconds.

An additional benefit of using Sentinel Hub is that the EO data can be accessed as a data cube using X-Cube. Data cubes
provide convenient access to a time series of satellite images, allowing computations across the time dimension, with
raster alignment issues handled out of the box. Data cubes will be used to provide information about changes over time
and space on the ECHOES platform. The Euro Data Cube (EDC) service [#Todo ref] is used to provide access data cubes. EDC
provides hosted Jupyter Notebooks for analysing EO data using Sentinel Hub and X-Cube. EDC is used for convenience and
is not required to access the data cubes, as X-Cube can be called directly.

The figure below shows a chain block diagram for the eo-mosaics processing chain, which generates GeoTIFFs using
Sentinel-Hub. The diagram shows the code running on a VM on CREODIAS; however, it is not limited to CREODIAS. For
example, it could run on AWS. An S3, compatible object store is required, but if one is not available Minio can be
used (see [Object Storage](#object-storage)). The code calls the Sentinel Hub API. The algorithm is implemented in
JavaScript.

![eo-custom-scripts block diagram](images/eo-custom-scripts-block-diagram.png)


The code in the [Sentinel-Hub Customs Scripts repository](https://github.com/sentinel-hub/custom-scripts), has been added
to the eo-mosaics repository, so that the script can called via the command line. This enables many EO products can be
quickly implemented on the ECHOES platform.

### eoian

## Object Storage

The results of the EO processing (e.g. GeoTiffs) are stored in an object store. A third Python
package, [eo-io](https://github.com/ECHOESProj/eo-io), is used
by [eo-custom-scripts](https://github.com/ECHOESProj/eo-custom-scripts)
and [eoian](https://github.com/ECHOESProj/eoian]) to store the results in S3. S3 is available on AWS and an S3
compatible object store is available on Creodias. When running locally, [Minio](https://min.io/) can be used to provide
an S3 compatible, locally hosted, object store.

## The Development Environment & Deployment

The setup of the earth observation environment can be time-consuming. Docker helps here. Both eo-custom-scripts and
eoian are containerised. It is therefore a matter of building the containers, as described in the README of these
packages. However, to get the EO service fully working we also need to build and run the websockets-server image and
eo-stack [#todo add section on websockets-server and eo-stack], in addition to handling the credentials.Docker ensures
that the code runs uniformly and consistently on the host machine or container service.

In general, the code is developed on local or virtual machines, rather than the Docker containers. There are a number of
steps involved in setting up the machine for development work.

### Automation of the dev environment using Ansible

Ansible is used to automate the setup of the development machines. 
The ansible playbooks are in the [eo-playbooks](https://github.com/ECHOESProj/eo-playbooks) repo.
See the README in the repo for the installation and usage instructions.

### Handling the credentials

The credentials are stored in yaml files in the eo-playbooks repository
and are encrypted and the key to decrypt them is stored in
the Compass account LastPass under Shared-ECHOES/eo-playbooks. 
The playbook is executed with the "--ask-pass" option, which prompts for the
password. 
The credentials will be copied over to the remote machine and unencrypted.
The credentials are stored in the eo-playbooks repo under:

    eo-playbooks/roles/servers_creodias/files/config_eo_service.yml
    eo-playbooks/roles/servers_no_s3/files/config_eo_service.yml
    eo-playbooks/roles/common/files/id_rsa

The first file contains the credentials for CREODAS, and the second is for running on a local machine. 
The third file is the 
[GitHub ssh key](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent).

If you want to use run the eo-custom-scripts and eoian without using Ansible, you will
need to decrypt the configuration files and copy the config files to the required directory (as described below).
[See here for instructions on decrypting encrypted files.](https://docs.ansible.com/ansible/latest/user_guide/vault.html#decrypting-encrypted-files)

Put the configuration file in the user's home directory, keeping the files names: config_eo_service.yml and config_eo_service.yml.

To run the code using Docker, copy the config files and GitHub key to the credentials directory,
for example, eo-custom-scripts\credentials. 
This is required because Docker cannot access files outside it's scope when building the image. 
The files in the credentials' directory are copied to the home directory in the container.

It is not necessary to carry out these steps manually if you use Ansible. 
However, if you are doing them manually, refer to the following Ansible role:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;eo-playbooks/roles/common/tasks/main.yml

for the steps involved in decrypting and copying the credentials across.
