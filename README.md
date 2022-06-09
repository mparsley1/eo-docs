# The ECHOES Earth Observation Processing Service

## Introduction

The ECHOES Earth Observation (EO) code is used to provide an EO service for the ECHOES web application. 

The processing chains have been developed to run in the cloud. Advantages of using cloud services, such as Creodias and AWS, include:
  * they can take advantage of the satellite data available there
  * the size of the VMs can be increased or decreased as required
  

## The Processing Packages 

There are two Python packages for EO processing with ECHOES: [eo-custom-scripts](https://github.com/ECHOESProj/eo-custom-scripts) and [eoian](https://github.com/ECHOESProj/eoian]). [eo-custom-scripts](https://github.com/ECHOESProj/eo-custom-scripts) uses the Sentinel-Hub API to generate products, whereas [eoian](https://github.com/ECHOESProj/eoian]) consumes satellite data stored in the object store (e.g. in the SAFE format for Sentinel-2 data). 

With eo-custom-scripts the processing is done on Sentinel-Hub's servers, whereas with eoian the processing is done locally. Therefore, the machine requirements may be greater for the eoian processing chain, depending on the processing. 

eoian and eo-custom-scripts can each be called by a CLI interface or imported as a Python module.

See the README of these packages for information on their installation and usage.  
          

## Object Storage

The results of the EO processing (e.g. GeoTiffs) are stored in an object store. A third Python package, [eo-io](https://github.com/ECHOESProj/eo-io), is used by [eo-custom-scripts](https://github.com/ECHOESProj/eo-custom-scripts) and [eoian](https://github.com/ECHOESProj/eoian]) to store the results in S3. S3 is available on AWS and an S3 compatible object store is available on Creodias. When running locally, [Minio](https://min.io/) can be used to provide an S3 compatible, locally hosted, object store. 



## The Development Environment & Deployment

The setup of the earth observation environment can be time-consuming. Docker helps here. Both eo-custom-scripts and eoian are containerised. It is therefore a matter of building the containers, as describe in the readme of these packages.   
