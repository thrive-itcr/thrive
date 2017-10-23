# Tumor Heterogeneity Research Interactive Visualization Environment (THRIVE)

Copyright (c) 2017 General Electric Company

_Development was supported by the National Cancer Institute of the National Institutes of Health under award number U01CA204826._


THRIVE is an interactive image analysis toolbox developed by University of Pittsburgh and GE Global Research under an NIH ITCR grant.

Included in the toolbox are algorithms of cell segmentation, cell quantification, and heterogeneity/diversity analysis.

Users can plug in their own algorithms, resulting in a flexible, powerful image analysis platform.

See the [THRIVE project web site](http://www.csb.pitt.edu/ith/index.html).  
 
 THRIVE is built using the __Rt. 106__ biomedical analytics framework, to be released imminently by GE Global Research.
 
## Features of THRIVE

* Includes a starter set of algorithms for cell segmentation, cell quantification, and heterogeneity metrics.
* A directory structure is defined for loading multi-channel pathology images from a variety of sources.
* Adaptors can be created to access existing data sources in place.
* Summary metrics are plotted interactively.

## Features of Rt. 106 (all available through THRIVE)

* Based on Docker for easy setup and configuration.
* Entirely web-based for easy deployment in cloud or on premise.
* Displays DICOM or TIFF images using the Cornerstone image viewer, along with mask overlays.
* An Algorithm SDK makes it easy to add new algorithms.
* The user interface is easily customizable to create custom applications.
* A file-based datastore is provided having a well-defined REST API so that alternative storage mechanisms can be integrated or developed.
* Algorithm executions are tracked in a database for later reference.
* Initial set of automated quality tests.

## THRIVE Roadmap

* Q4 2017
  * Initial deployment in github and Dockerhub to solicit community feedback.
  * Runs against provided sample dataset.
* Q1 2018
  * Tools for uploading user-provided pathology images.
  * Additional algorithms for intra-cellular heterogeneity.
  * Expanded set of automated system tests to ensure quality.
* Q2 2018
  * Hands-on support for beta users to contribute more heterogeneity algorithms.
  * Tools for capturing community feedback on algorithms.
* 2018+ (to be prioritized based on user community input)
  * Machine-learning capability to classify cell types.
  * Whole-slide image viewing.
  * Whole-slide image analytics.
  * Automatically provisioning processors for large cohort studies.
  * Richer image display capabilities.  e.g. color blends
  * Support for other data types.  e.g. FISH
  
--------------------------------

Please send inquiries to [Brion Sarachan (GE)](mailto:sarachan@ge.com) and [Chakra Chenubhotla (UPitt)](mailto:chakracs@pitt.edu)




