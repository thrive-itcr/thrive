# Setting up THRIVE
This document tells you how to set up your own local installation of THRIVE.

## Prerequisites
* You need to have Docker running on your system, including Docker Compose.  Docker is cross platform and can be used on Linux, Mac, and Windows. On Windows and Mac, we recommend Docker for Windows and Docker for Mac over the older Docker Toolbox.
* You should clone this git repository located at https://github.com/thrive-itcr/thrive.  

## Unpack demo data
Create a directory where you would like to keep your demo data.  This can be called any directory that you choose.  For these examples, let's assume you have created a subdirectory called **THRIVEdata**.

Download the **pathology_sample_data.tar.gz** file from [this web address](https://pitt.box.com/shared/static/dkcp4fshs3qid258kyh1w497n2320fij.gz) into your **THRIVEdata** directory.

Unzip and untar pathology_sample_data.tar.gz.  On Linux based systems, this can be done with the command **tar -xzf pathology_sample_data.tar.gz**.

You should now have a subdirectory called **Slides** within your **THRIVEdata** directory.

You should have a directory structure like this:
* THRIVEdata *(or directory name of your choice)*
  - Slides
    - (slide data here)
    
## Build Rt.106 Docker images

THRIVE leverages the Rt.106 analytics platform.  You will need a subset of the Rt. 106 Docker images.
There are two different ways to achieve this: (1) clone the source code for Rt.106 and build the Docker images
youself, or (2) download the Docker images from the [Rt106 Dockerhub repository](https://hub.docker.com/u/rt106/).

For (2), you don't need to do anything at this stage.

You only need to use approach (1) if you intend to modify Rt106 itself.  To proceed, 
you will need to clone the following repositories from the [Rt106 github site](https://github.com/rt106) 
and build their corresponding Docker images as per the README file in each repository:

* rt106-algorithm-sdk
* rt106-wavelet-nuclei-segmentation
* rt106-datastore
* rt106-datastore-local
* rt106-mysql
* rt106-rabbitmq
* rt106-frontend

There is an order dependency among some of these.  If you build the images in the sequence shown,
that will satisfy the order dependency.

rt106-wavelet-nuclei-segmentation has a two step build process.  The first step compiles a number of third-party
libraries and may take 30 minutes or more.  The other builds should all be fairly quick.

## Build THRIVE Docker images

For THRIVE Docker images, you have the same two choices as for Rt.106: (1) clone the source code for THRIVE and build 
the Docker images yourself, or (2) download the Docker images from the THRIVE Dockerhub repository.  __NEED TO CREATE THRIVE DOCKERHUB REPO__

Again, for option (2), you don't need to do anything at this stage.

You only need to use approach (1) if you intend to modify the THRIVE application or supplied algorithms.
To proceed, you will need to clone the following repositories from the 
[THRIVE github site](https://github.com/thrive-itcr) 
and build their corresponding Docker images as per the README file in each repository:

__Newer DOCKERFILE Conventions?__

__Add NO_PROXY cases to README files__

* __NEED TO ADD Whole_Cell_Segmentation__
* multicompartment-cell-quantification
* simple-heterogeneity-metrics
* thrive-app   __different image name?__

## Set up environment variables

If you did not create a clone of the thrive-app repository in the previous step,
please do so now.

Within that directory, create a file called .env that contains this line:

```
LOCAL_DATA_DIR=/your/data/root
```

There are other environment variable options, but this setting will allow you to run the basic THRIVE application.

Be sure to set /your/data/root to the path at the top of your data directory structure from the previous step, perhaps ending in __THRIVEdata__.

Windows paths  will start with a drive letter (e.g. "C:").  Forward slashes in the path are fine on Windows. 

## Run THRIVE

In your thrive-app directory, you should now be able to successfully run:

```
docker-compose up
```

This command should either run your locally-built Docker images or download them (the first time)
from Docker Hub, depending on whether you built the images in the steps above.

In your console you will see many diagnostic messages from all the Docker containers.  This is normal.
Startup may take a couple of minutes, especially the first time which sets up and initializes the THRIVE database.

## Try it out
Assuming the steps above were successful, start a web browser (Chrome recommended).  Go to http://localhost:81.  You should see THRIVE running in the browser.

## Shut down THRIVE

To exit the THRIVE application, you need to shut down the server.  In the console
where you previously ran 'docker-compose up' type ctrl-C a couple of times to 
shut down the THRIVE server.

It is good practice to also clean up docker by running:
```
docker-compose down
```
