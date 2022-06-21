
# The ECHOES Earth Observation Processing Service

# Contents
 - [Introduction](#introduction)
 - [The Processing Packages](#the-processing-packages) 
 - [The eo-custom-scripts package](#the-eo-custom-scripts-package) 
 - [Object Storage](#object-storage)

## Introduction

The ECHOES Earth Observation (EO) code is used to provide an EO service for the ECHOES web application. The EO service is decoupled for the web service and can be used independently of it 

The processing chains have been developed to run in the cloud. Advantages of using cloud services, such as Creodias and AWS, include:
  * they can take advantage of the satellite data available there
  * the size of the VMs can be increased or decreased as required
  

## The Processing Packages

There are two Python packages for EO processing with ECHOES: [eo-custom-scripts](https://github.com/ECHOESProj/eo-custom-scripts) and [eoian](https://github.com/ECHOESProj/eoian]). [eo-custom-scripts](https://github.com/ECHOESProj/eo-custom-scripts) uses the Sentinel-Hub API to generate products, whereas [eoian](https://github.com/ECHOESProj/eoian]) consumes satellite data stored in the object store (e.g. in the SAFE format for Sentinel-2 data). 

With eo-custom-scripts the processing is done on Sentinel-Hub's servers, whereas with eoian the processing is done locally. Therefore, the machine requirements may be greater for the eoian processing chain, depending on the processing. 

eoian and eo-custom-scripts can each be called by a CLI interface or imported as a Python module. See the README of these packages for information on their installation and usage.  
              
### The eo-custom-scripts package

[Sentinel Hub](https://www.sentinel-hub.com/) is a multi-spectral and multi-temporal big data satellite imagery service. Users can use APIs to 
retrieve satellite data over their AOI and specific time range from full archives in a matter of seconds.

An additional benefit of using Sentinel Hub is that the EO data can be accessed as a data cube using X-Cube. Data cubes provide convenient access to a time series of satellite images, allowing computations across the time dimension, with raster alignment issues handled out of the box. Data cubes will be used to provide information about changes over time and space on the ECHOES platform. The Euro Data Cube (EDC) service [#Todo ref] is used to provide access data cubes. EDC provides hosted Jupyter Notebooks for analysing EO data using Sentinel Hub and X-Cube. EDC is used for convenience and is not required to access the data cubes, as X-Cube can be called directly.

 ![Tux, the Linux mascot](images/eo-custom-scripts-block-diagram.png)

The figure above shows a chain block diagram for the eo-mosaics processing chain, which generates GeoTIFFs using Sentinel-Hub. The diagram shows the code running on a VM on CREODIAS; however, it is not limited to CREODIAS. For example, it could run on AWS. An S3, compatible object store is required, but if one is not available Minio can be used (see [Object Storage](#object-storage)). The code calls the Sentinel Hub API. The algorithm is implemented in JavaScript.

The code in the Sentinel-Hub Customs Scripts repository, https://github.com/sentinel-hub/custom-scripts, has been added to the eo-mosaics repository, so that the script can called via the command line. This enables many EO products can be quickly implemented on the ECHOES platform.
                       

### Eoian




## Object Storage

The results of the EO processing (e.g. GeoTiffs) are stored in an object store. A third Python package, [eo-io](https://github.com/ECHOESProj/eo-io), is used by [eo-custom-scripts](https://github.com/ECHOESProj/eo-custom-scripts) and [eoian](https://github.com/ECHOESProj/eoian]) to store the results in S3. S3 is available on AWS and an S3 compatible object store is available on Creodias. When running locally, [Minio](https://min.io/) can be used to provide an S3 compatible, locally hosted, object store. 


## The Development Environment & Deployment

The setup of the earth observation environment can be time-consuming. Docker helps here. Both eo-custom-scripts and 
eoian are containerised. It is therefore a matter of building the containers, as describe in the readme of these 
packages. However, to get the EO service fully working we also need to build and run the websockets-server image and 
eo-stack [#todo add section on websockets-server and eo-stack], in addition to handling the credentials.

The credentials are stored encrypted in the repository and the key to decrypt them is stored in LastPass under Shared-ECHOES/eo-playbooks.  
