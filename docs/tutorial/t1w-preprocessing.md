!!! warning
    This is a draft

## Anatomical (T1w) preprocessing.

This page demonstrates common steps used to preprocess anatomical magnetic resonance imaging data (T1-weighted or T1w) on brainlife.io The goal of this tutorial is to show you how to process anatomical data for successive analyses, including volumetric analyses from T1w meausres and a combination of T1w and diffusion-weighted MRI (dMRI) or functional neuroimaging data (fMRI) pipelines.

This tutorial will use a combination of skills developed in the [Introduction tutorial](https://brainlife.io/docs/tutorial/introduction-to-brainlife/). If you have not read the tutorial, or you are not comfortable staging, processing, archiving, and viewing data on brainlife.io, please go back and read that tutorial before beginning this one.

<iframe width="560" height="315" src="https://www.youtube.com/embed/hC0Ms3KWD8o" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### 1. Set the orientation of the brain.

The first step is to make sure the anatomy of the brain "looks right." This means that the coordinate system for the T1w image is stored rotated such that the brain appears up-right and centered when opened in the major visualization toolboxes that the scientific community uses. We typically look at images where the nose faces toward the right-hand side in the sagittal plane and faces forward in the axial and coronal planes. This rotation involves changing information in the T1w data files and headers. Beside agreeing to the standard convetions, this orientation is important because it ensures that the data inside the T1w images will be **indexed**,  or read, appropriately -- allowing different software libraries for neuroimaging data analysis to  read the data and interpret the position and dimensions of individual three-dimensional pixels, called **voxels**. During this process, the neck is often removed from the original MRI data to fit the image within an adequate volume.  

Useful information about MRI image orientation can be found at: 
  - [https://fsl.fmrib.ox.ac.uk/fsl/fslwiki/Orientation%20Explained](https://fsl.fmrib.ox.ac.uk/fsl/fslwiki/Orientation%20Explained) 
  - [http://gru.stanford.edu/doku.php/mrTools/coordinateTransforms](http://gru.stanford.edu/doku.php/mrTools/coordinateTransforms)
  
The best [brainlife.io](https://brainlife.io) app to perform this task is the Crop and Reorient T1 app:

| ![crop-reorient](/docs/img/app.crop-reorient.bl.header.png)|
|------------------------------------|
| https://doi.org/10.25663/bl.app.15 |

For this tutorial, we will use this app to -- you guessed it -- crop and reorient the anatomical (T1w) image.

To perform a crop and reorientation of the raw anatomical (T1w) image, follow these steps:

1. Go to the 'Archives' page of your project by clicking the 'Archives' tab on your Projects page.
1. Select the raw (i.e. untouched) neuro/anat/t1w datatype by clicking the box next to the dataset.
1. Click the 'Stage to process'
    * For Project, make sure your project is selected
    * For Process, select 'Create New Process'
    * For Description, name the process 'Anatomical Preprocessing'
    * Hit 'OK'
1. On the 'Process' tab, click 'Submit App' to submit a new application.
    * In the search bar, type 'Crop and Reorient T1'.
    * Click the app card.
1. On the 'Submit App' page, select the following:
    * For input, select the staged raw anatomical (T1w) image by clicking the drop-down menu and finding the appropriate dataset.
    * Select the boxes for 'reorient' and 'crop'
    * Select the box for 'Archive all output datasets when finished'
        * For 'Dataset Tags', type and enter 'crop_reorient'
    * Hit 'Submit'
1. Once the app is finished running, view the results by clicking the 'eye' icon to the right of the dataset
    * Choose 'fsleyes' as your viewer

If you're happy with the results, then you're ready to move onto the next step: ACPC alignment!

### 2. Anterior Commissure - Posterior Comissure (ACPC) alignment.

The next step is to align the anatomical image to the point where two white matter tracts cross hemispheres: the **anterior commissure** and **posterior commissure** (ACPC). The anterior commissure connects the two temporal lobes and is located below the anterior portion of the **corpus callosum**, the large white matter tract connecting the two hemispheres. The posterior commissure is located just behind the **cerebral aqueduct**, the important connection between the third and fourth ventricles. 

This alignment step will move the image in a way that effectively "centers" it. Specifically, the image will be moved so that a horizontal line drawn from the front to the back of the brain will pass through both landmarks, while a horizontal line drawn from one temporal lobe to another and a vertical line from the top of the brain to the bottom bisect at the midpoint of the two landmarks. A majority of the following processing steps require the anatomical image to be aligned to the ACPC plane, including Freesurfer parcellation. 

[brainlife.io](https://brainlife.io) provides two apps that can be run on raw T1w to align the image to the ACPC plane automatically. One app uses the Automatic Registration Toolbox (ART) and the other uses FSL and the Human Connectome Project (HCP) preprocessing pipeline. We recommend using the HCP-based app as it is a modern and generally performs better than ART.

The first app from the Automated Registration Toolbox (ART) is called **ACPC alignment via ART**: 

| ![art](/docs/img/app.art.bl.header.png)|
|------------------------------------|
| https://doi.org/10.25663/bl.app.16 |

However, the best [brainlife.io](https://brainlife.io) app to perform this task is **HCP ACPC ALignmnet (T1)**:

| ![hcp-acpc](/docs/img/app.hcp-acpc.bl.header.png)|
|------------------------------------|
| https://doi.org/10.25663/bl.app.99 |

As such, we will use the HCP ACPC Alignment app in this tutorial.

To perform ACPC alignment of the cropped and reoriented anatomical (T1w) image, follow these steps:

1. From the same process you used for Step 1 (crop and reorient), click 'Submit App' to submit a new application.
    * In the search bar, type 'HCP ACPC Alignment (T1w)'.
    * Click the app card.
1. On the 'Submit App' page, select the following:
    * For input, select the cropped and reoriented anatomical (T1w) image by clicking the drop-down menu and finding the appropriate dataset (look for the datatag 'crop_reorient').
    * For 'template', choose the 'MNI152_1MM' template from the dropdown menu.
    * For 'reorient', deselect this box as we have already reoriented the image.
    * Select the box for 'Archive all output datasets when finished'
        * For 'Dataset Tags', type and enter 'acpc_aligned'
    * Hit 'Submit'
1. Once the app is finished running, view the results by clicking the 'eye' icon to the right of the dataset
    * Choose 'fsleyes' as your viewer

If you're happy with the results, then you're ready to move onto the next step: Freesurfer parcellation!

### 3. Freesurfer Brain Parcellation - Generation.

The next step is to perform anatomical preprocessing and **parcellate**, or divide, the brain into different regions and tissue types, such as white matter or gray matter, using Freesurfer. These regions represent different gray matter landmarks, such as the motor and somatosensory cortices, and are typically derived from histological properties of these regions. Sometimes, they are derived from functional activation (i.e. fMRI) patterns. Freesurfer will divide the anatomical image into different tissue types, parcellate the gray matter into known anatomical landmarks, and output statistics regarding the volume and thickness of the different tissue types and anatomical landmarks. These regions are then used for a large number of downstream steps, including white matter tract segmentation. Information regarding the volume and thickness of each region can also be used for group analyses.

The best [brainlife.io](https://brainlife.io) app to perform this task is Freesurfer:

| ![freesurfer](/docs/img/app.freesurfer.bl.header.png)|
|------------------------------------|
| https://doi.org/10.25663/bl.app.0 |

To perform Freesurfer parcellation of ACPC-aligned anatomical (T1w) image, follow these steps:

1. From the same process you used for Step 1 (crop and reorient) & Step 2 (ACPC alignment), click 'Submit App' to submit a new application.
    * In the search bar, type 'Freesurfer'.
    * Click the app card.
1. On the 'Submit App' page, select the following:
    * For input, select the ACPC-aligned anatomical (T1w) image by clicking the dropdown menu and finding the appropriate dataset (look for the datatag 'acpc_aligned').
    * Select the boxes for 'hippocampal' and 'hires'.
    * For 'version', select '6.0.0' from the drop-down menu.
    * Select the box for 'Archive all output datasets when finished'
        * For 'Dataset Tags', type and enter 'freesurfer'
    * Hit 'Submit'
1. Once the app is finished running, view the results by clicking the 'eye' icon to the right of the dataset
    * Choose 'freeview' as your viewer
        * This will load the following volumes and surfaces: aseg, brainmask, white matter mask, T1, left/right hemisphere pial (cortical) and white (white matter) surfaces.
    * To view the aparc.a2009s segmentation on an inflated surface, do the following:
        * Click File --> Load surface
            * Choose the lh.inflated and rh.inflated surfaces
            * Hit 'OK'
        * Select inflated surface of choice (i.e. left or right hemisphere)
        * Click the drop-down menu next to 'Annotation' and choose 'Load from file'
            * Choose the appropriate hemisphere aparc.a2009s.annot file (i.e. lh.aparc.a2009s.annot)
            * Hit 'OK'
            * The aparc.a2009s parcellation should be overlayed on your inflated surface! Repeat the process on the other hemisphere.

If you're happy with the results, then you've successfully processed the anatomical (T1w) datatype! You are now ready to collate statistics into a .csv file for group-level analysis!

### 4. Freesurfer Brain Parcellation - Statistics.

The next step is to take the parcellation generated in Step 3 and collate the important statistics into a .csv file for easy viewing and implementation of group analyses. Within each region of the parcellation, called an ROI, or a **region of interest**, Freesurfer computes simple statistics regarding the number of vertices, surface area, volume, cortical thickness, mean and gaussian curvatures, and the fold and curvature indices. These statistics can be used to perform group-level analyses, including group differences within ROIs and correlation analyses with behavioral data.

The best [brainlife.io](https://brainlife.io) app to perform this task is Freesurfer Statistics:

| ![freesurfer](/docs/img/app-freesurfer-stats.bl.header.png)|
|------------------------------------|
| https://doi.org/10.25663/brainlife.app.272 |

To collate the statistics from the Freesurfer output generated in Step 3, follow these steps:

1. From the same process you used for Step 1 (crop and reorient), Step 2 (ACPC alignment), and Step 3 (Freesurfer brain parcellation - generation), click 'Submit App' to submit a new application.
    * In the search bar, type 'Freesurfer Statistics'.
    * Click the app card.
1. On the 'Submit App' page, select the following:
    * For input, select the Freesurfer output generated in Step 3 by clicking the drop-down menu and finding the appropriate dataset (look for the datatag 'freesurfer'.
    * For parcellation, please choose the parcellation of your choice. For this tutorial, we will use the 'aparc.a2009s'. To choose this, select the 'aparc.a2009s' option from the dropdown menu.
    * Select the box for 'Archive all output datasets when finished'
        * For 'Dataset Tags', type and enter 'freesurfer-stats'
    * Hit 'Submit'
    
To view the results, download the data by clicking the 'download' button next to the dataset. Then open the 'lh' and 'rh' csv files into your favorite spreadsheet of choice -- now, you're ready to start your group analyses!

Congrats, you finished preprocessing your anatomical (T1w) image! You are now ready to move onto the next tutorial: Resting state (fMRI) processing.
