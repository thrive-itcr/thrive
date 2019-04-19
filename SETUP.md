# Setting up THRIVE
This document tells you how to set up your own local installation of THRIVE.

## Prerequisites
* You need to have Docker running on your system, including Docker Compose.  Docker is cross platform and can be used on Linux, Mac, and Windows. On Windows and Mac, we recommend Docker for Windows and Docker for Mac over the older Docker Toolbox.
* For managing local files against the THRIVE source code in github, you should have a git client.
* You should clone [this git repository](https://github.com/thrive-itcr/thrive) for documentation files and the Docker Compose script needed to run THRIVE.  

## Unpack demo data
Create a directory where you would like to keep your demo data.  This can be called any directory that you choose.  For these examples, let's assume you have created a subdirectory called **THRIVEdata**.

Download the **pathology_sample_data.tar.gz** file from [this web address](https://pitt.box.com/shared/static/dkcp4fshs3qid258kyh1w497n2320fij.gz) into your **THRIVEdata** directory.

Unzip and untar pathology_sample_data.tar.gz.  On Linux based systems, this can be done with the command **tar -xzf pathology_sample_data.tar.gz**.

You should now have a subdirectory called **Slides** within your **THRIVEdata** directory.

You should have a directory structure like this:
* THRIVEdata *(or directory name of your choice)*
  - Slides
    - (slide data here)
    
## Set up environment variables

Within your directory that contains the file docker-compose.yml (see Prerequisites above),
 create a file called .env that contains this line:

```
LOCAL_DATA_DIR=/your/data/root
Rt106_SERVER_HOST=localhost
```

(There are other environment variable options, but this setting will allow you to run the basic THRIVE application.)

NOTE:  Setting Rt106_SERVER_HOST to hostname will enable you to run on your same computer.  If 
you instead set Rt106_SERVER_HOST to be a hostname or IP address, you can run THRIVE as a remote web-based server.

Be sure to set /your/data/root to the path at the top of your data directory structure from the previous step, perhaps ending in __THRIVEdata__.

Windows paths  will start with a drive letter (e.g. "C:").  Forward slashes in the path are fine on Windows. 

## Run THRIVE

In your thrive directory, you should now be able to successfully run:

```
docker-compose up
```

The first time you run this command, the needed Docker images will be downloaded from Docker Hub.

In your console you will see many diagnostic messages from all the Docker containers.  This is normal.
Startup may take a couple of minutes, especially the first time which sets up and initializes the THRIVE database.

## Try it out
Assuming the steps above were successful, start a web browser (Chrome recommended).  Go to http://localhost.  You should see THRIVE running in the browser.

For a sample scenario that you can run with THRIVE, see the sample video.  (Coming soon!)

If you experience any issues with THRIVE, for the time being, please contact [Brion Sarachan](mailto:sarachan@ge.com) and [Chakra Chennubhotla](mailto:chakracs@pitt.edu).
In the near future we will be establishing a more formal method for logging issues.

## Shut down THRIVE

To exit the THRIVE application, you need to shut down the server.  In the console
where you previously ran 'docker-compose up' type ctrl-C a couple of times to 
shut down the THRIVE server.

It is good practice to also clean up docker by running:
```
docker-compose down
```
A convenient use of the multiprocessing capability is the "Process All Regions" button
which iterates through all regions on the current slide and submits requests to run
the selected algorithm.  The THRIVE architecture scales to use all running Docker containers
in parallel.

## Multiprocessing

THRIVE can be launched with multiple instances of any of its Docker containers that
perform analysis.  For example to run with 5 copies of the cell quantification
Docker container and 5 copies of the container that calculates simple heterogeneity
metrics, use this command to start the system:
```
docker-compose up --scale multi-compartment-cell-quantification=5 --scale simple-heterogeneity-metrics=5
```
The names of the containers are the names used in the docker-compose.yml file.

The system is shut down in the same way as before:
```
docker-compose down
```


## Developing new capabilities for THRIVE

You could contribute capabilities to THRIVE in several ways:
* New heterogeneity (or related) algorithms.
* New visualization or display capabilities.
* Improvements to existing platform.

Each of these will be briefly described below.

For any of these changes, please coordinate with the THRIVE team 
(contacts [Brion Sarachan](mailto:sarachan@ge.com) and [Chakra Chennubhotla](mailto:chakracs@pitt.edu)) on how to have your changes 
added to the THRIVE release.

### Adding a new algorithm.

Please refer to [ALGORITHM_SDK.md](https://github.com/thrive-itcr/thrive/blob/master/ALGORITHM_SDK.md) in github
or in your own clone of the github THRIVE repository.

### Adding a new visualization of display capability.

These changes would likely be in [thrive-app](https://github.com/thrive-itcr/thrive-app).
Please clone that git repository and refer to the 
[Rt106 Custom Application](https://github.com/rt106/rt106.github.io/blob/master/CUSTOM_APPLICATION_SDK.md) documentation.

### Improvements to the existing platform.

These may be the most complex changes that you would make.  If you are interested, you have
access to all the open source code for [THRIVE](https://github.com/thrive-itcr)
and its underlying [Rt106](https://github.com/rt106) framework.  We suggest contacting the
THRIVE or Rt106 teams to discuss what you have in mind.


