# How to Add your Own Algorithms to THRIVE

_THRIVE is built as a custom application on the open source Rt 106 biomedical analytics framework (to be released Q4 2017).  Adding new algorithms to THRIVE leverages the plug-in mechanism of Rt. 106, which is described here._

Route 106 has been designed to be as easy as possible to add your own algorithms.  The procedure for adding an algorithm would be expected to take 2-3 hours, depending on the complexity of your algorithm.

(If your algorithm follows a vastly different paradigm or workflow than has previous been integrated into the Rt. 106 platform, requiring changes to the platform itself, the process may take longer.)

Rt. 106 is Docker-based.  Each system component and each integrated algorithm runs in a Docker container.

An algorithm container contains the algorithm itself and an **adaptor** that interfaces the algorithm to the rest of Rt. 106.  The adaptor has a generic part and an application-specific part.  Therefore, an algorithm container consists of three layers:

* generic adaptor
* application-specific adaptor
* algorithm

Here is a brief description of each of these layers:

**generic adaptor** - This code receives requests to run the algorithm from the Rt. 106 internal messaging system and returns status and response data to the messaging system.  It also downloads data from and uploads data to Rt. 106's datastore.  

The generic adaptor has been designed to include as much of the adaptor functionality as possible, and *does not need to be changed* to integrate your algorithm.  You do not need change the generic adaptor in any way to perform your integration.

**application-specific adaptor** -- This is the minimal adaptor code and metadata that is specific for your algorithm.  The documentation below will guide you through the steps to build your own application-specific adaptor.

**algorithm** -- Of course this is your algorithm that you integrate with Rt. 106.

The steps required to integrate you algorithm are:

0. Set up needed Rt. 106 files.
1. Create an algorithm container for your algorithm.
2. Define your algorithm's metadata.
3. Write a short wrapper script for your algorithm.
4. Add your algorithm container to the Docker Compose network.

These steps are described below.  Rt. 106 includes two complete examples:
* simple region growing - an example for medical imaging / radiology
* wavelet-based nuclear segmentation - an example for pathology / microscopy

## What types of algorithms are appropriate for Rt. 106?
Rt. 106 is intended for algorithms that execute in batch mode.  In other words, the algorithms are expected to receive inputs and parameters and to produce outputs, but should not have interactive / user interface code intermixed with the algorithm.

Rt. 106 can be used to create interactive *applications* by building the interactive elements into a custom user interface.  The algorithms themselves should still be structured as batch algorithms having no user interfaces of their own.

Rt. 106 puts no contraints on the implementation language used to build an algorithm.  Any algorithm that can run in a Docker container can be integrated.

If you have special hardware requirements, such as GPUs, please contact the Rt. 106 developers.  We would like to explore these issues with you.

## 1.  Set up needed Rt. 106 files.
You need to start with a subset of the files in the rt106-algorithm-template git repository.  Clone the rt106-algorithm-template repository and copy these files into your own directory that contains your algorithm:
* entrypoint.sh
* rt106GenericAdaptorAMQP.py
* rt106GenericAdaptorREST.py
* rt106SpecificAdaptorCode.py
* rt106SpecificAdaptorDefinitions.json

## 2. Create an algorithm container for your algorithm.
Docker is a pervasive open-source containerization technology.  Docker's own website contains much documentation (https://docs.docker.com/) and many other references on Docker are available.

This guide will describe the specific details of creating your algorithm's Docker container for Rt. 106.

Your algorithm will need a Dockerfile that builds your algorithm within a Docker container along with the Rt. 106 adaptor code.  See https://docs.docker.com/engine/reference/builder/ for details on Dockerfiles.

As an example, here is the Dockerfile used for the simple region growing example algorithm:

```
FROM rt106/rt106-algorithm-template

ADD simpleRegionGrowing.tar.gz /rt106/bin

ADD rt106SpecificAdaptorCode.py /rt106/rt106SpecificAdaptorCode.py
ADD rt106SpecificAdaptorDefinitions.json /rt106/rt106SpecificAdaptorDefinitions.json

WORKDIR /rt106

CMD ["/rt106/entrypoint.sh"]
```
A few aspects of the Dockerfile are required:
* The base image for all Rt. 106 algorithm containers is rt106/rt106-algorithm-template.  This base image contains the generic adaptor described above, and also a Python environment.
* rt106SpecificAdaptorCode.py and rt106SpecificAdaptorDefinitions.json, which you will modify below, are included as shown.
* The WORKDIR should be /rt106 as shown.
* entrypoint.sh needs to be called as shown.

Beyond these details, the Dockerfile must build your algorithm.  (The base image is based on Debian Linux.  Please contact the Rt. 106 developers if this will not work for your algorithm.)

For algorithms implemented as Python scripts, you may be able to simply copy your source files into the container using the ADD command.  For more complex algorithms implemented using compiled code and class libraries, you may want to refer to the implementation of the simple region growing algorithm example, which uses a "dev" Docker image to create the executable which is then simply added to the "ops" Docker image as shown in the Dockerfile above using this line:
* ADD simpleRegionGrowing.tar.gz /rt106/bin

## 3. Define your algorithm's metadata.
There is certain metadata about your algorithm that you need to specify.  This metadata is used to automatically generate appropriate user interface components.  You can therefore run your algorithm in an existing Rt 106 application without writing new user interface code.  Of course, you also have the flexibility to create a custom, specialized user interface if that is appropriate for your application.  Doing so does require some custom implementation of the front end of your application.

This approach borrows heavily from the approach used in 3D Slicer (https://www.slicer.org/), although the web technologies used are more up-to-date (e.g. JSON rather than XML).

Metadata required for an algorithm includes:
* name
* version
* queue name
* classification (so that an app can filter for algorithm types)
* inputs and parameters
* results
* how to display image results

This metadata is documented using a JSON format in rt106SpecificAdaptorDefinisions.json.  Several examples are provided in _rt106-algorithm-template_, _simple-region-growing_, and _wavelet-nuclear-segmentation_.  Please refer to these as a starting point for your own algorithm's metadata.

Please refer to REFERENCE.md for a detailed description of all the JSON metadata fields for algorithm integration.

## 4. Write a short wrapper script for your algorithm.

For your application, you will modify rt106SpecificAdaptorCode.py (copied above to your algorithm's directory), to add details to the Python function run_algorithm() which is called by the Rt 106 platform when there is a request to run your algorithm.  The function needs to:
* Gather the needed data from the datastore, checking for errors such as missing data.
* Call your algorithm, checking for errors that may be returned.
* Store generating result data back in the datastore.
* Return status and data back to the caller.

Please refer to the supplied examples in _rt106-algorithm-template_, _simple-region-growing_, and _wavelet-nuclear-segmentation_.  You will be filling out this Python function, which is part of rt106SpecificAdaptorsCode.py:

```
run_algorithm(datastore, context)
```

__datastore__ is an object that provides a number of functions for accessing the Rt. 106 datastore.  Please refer to REFERENCE.md for its API.

__context__ is an object that should contain all the _parameters_ that you defined in your JSON metadata file.  For example, if your JSON file includes a parameter called _inputSeries_, within run_algorithm you should have a variable _context[inputSeries]_ available to you.

Your function should return a Python object (JSON structure) that includes your result data and execution status.  The last line of your function will likely look something like this:

```
return { 'result' : result_context, 'status' : status }
```

__result_context__ should be an object structure that includes fields for each element of the _results_ section of your JSON file.

__status__ may simply be a string such as "EXECUTION_FINISHED_SUCCESS" or an appropriate error message.

Recall that your algorithm and adaptor will run within the Docker container created in step 2 above.  You will have a Python environment available to you.  If you require any special libraries or packages for your algorithm or managing its data, you should add the setup for these to your Dockerfile.

### entrypoint.sh

Another required file for your _application-specific adaptor_ is entrypoint.sh.  This is the shell script that is called to launch your algorithm container.  This script sets up a few things including the location of the datastore and the type of queuing system used.  Please refer to the _entrypoint.sh_ file in the _simple region growing_ algorithm example for the form of _entrypoint.sh_.  You may be able to use this script verbatim.

## 5. Add your algorithm container to the Docker Compose network.

To run any of the Rt. 106 example applications or a custom application that you have created, you will run a Docker Compose file.  Your algorithm needs to be added as a new service in your Docker Compose file, according to this template:
```
  myalgorithm:
    image: rt206/my-algorithm:latest
    ports:
    - 7106
    environment:
      MSG_SYSTEM: amqp
      SERVICE_NAME: myalgorithm--v1_0_0
      SERVICE_TAGS: analytic

```
There are a couple of options for organizing your Docker Compose file(s).  Your application can have a single docker-compose.yml file.  A benefit of this are simple commands for managing your application.  For example, you will be able to launch your application by simply typing:
```
docker-compose up
```
Another option is to use multiple Docker Compose files, for example with the core of Rt. 106 described in _docker-compose.yml_ and the containers that are specfic to your application in _docker-compose.myapp.yml_.  In this case you would start the system by typing:
```
docker-compose -f docker-compose.yml -f docker-compose.myapp.yml up
```
These are standard options for Docker Compose and are further documented here:  https://docs.docker.com/compose/ 

-----------------------------------------------------------------------------------------


For questions and help about these instructions, please contact [Jim Miller](mailto:millerjv@ge.com) and / or [Brion Sarachan](mailto:sarachan@ge.com).
