## The HCP multi-modal parcellation 1.0 atlas in FSL

The purpose of this repository is to offer a 'quick and dirty' way to visualize and query the Human Connectome Project multi-modal parcellation 1.0 (HCP-MMP1) atlas in FSL (version > 6.0), and to compare it with results that were originally reported in MNI coordinates, results of fMRI meta-analytic techniques, or to examine correspondences with other parcellations.
In particular, after importing the atlas in FSL, it will be possible to visualize it in FSLeyes and to use the atlasquery utility to query the atlas from the terminal.

**Disclaimer**: Please note that this project is not meant to endorse or encourage in any way the usage of the HCP-MMP1 atlas for new analyses performed using volumetric registration techniques. The motivations are thoroughly discussed in Coalson, T. S., Van Essen, D. C., & Glasser, M. F. (2018). The impact of traditional neuroimaging methods on the spatial localization of cortical areas. Proceedings of the National Academy of Sciences of the United States of America, 115(27), E6356–E6365. (https://doi.org/10.1073/pnas.1801582115). Therefore, I decline any responsibility that would potentially result from misinterpretation or incorrect usage of the data and the methods presented in this project.

For an accurate method of mapping existing data from the MNI152 standard to a surface template compatible with the MMP1 atlas, such as Fsaverage, please consult the following projects and documentation:

- https://github.com/ThomasYeoLab/CBIG/tree/master/stable_projects/registration/Wu2017_RegistrationFusion
- https://figshare.com/articles/HCP-MMP1_0_projected_on_fsaverage/3498446
- https://wiki.humanconnectome.org/download/attachments/63078513/Resampling-FreeSurfer-HCP.pdf

I also point the interested reader to this [figshare article](https://figshare.com/articles/HCP-MMP1_0_projected_on_MNI2009a_GM_volumetric_in_NIfTI_format/3501911) which, to the best of my knowledge, was the first to publish a method to map the MMP1 atlas to the MNI space.

Finally, please always refer to the [FSL wiki](https://fsl.fmrib.ox.ac.uk/fsl/fslwiki/Atlases) for an updated list of the available atlases.

### Description of the process (for instructions on how to import the atlas jump to the [next paragraph](#Importing-the-atlas-in-FSL))

The volumetric version of the MMP1 atlas was released as part of the [AFNI software](https://sscc.nimh.nih.gov/pub/dist/doc/misc/history/afni_hist_level3_all.html).

Release note: 05 Apr 2019, DR Glen, whereami, level 3 (MAJOR), type 3 (NEW_ENV)
    HCP Glasser atlas in AFNI atlases.

    Mike Beauchamp and Meghan Robinson contributed atlas. Now included and first in default list too
    This atlas is in MNI space (not in its original Contee
    grayordinate surface space.
    Additionally, all the pmaps (probability maps) have been
    removed from the standard distribution and default list

At the time of writing, this corresponds to:

```
$ afni -ver
$ Precompiled binary Debian-x86_64-1-gnu: May 17 2018 (Version Debian-18.0.05+git24-gb25b21054~dfsg.1-1~nd17.10+1+nd18.04+1)
```

The process of mapping the MMP1 atlas to the MNI152 template is described [here](https://openwetware.org/wiki/Beauchamp:CorticalSurfaceHCP).

The volumetric version of the MMP1 atlas was initially downloaded from the [AFNI repository](https://afni.nimh.nih.gov/pub/dist/tgz/atlases_latest.tgz).
The MMP1 atlas has 180 labels per hemisphere, each associated with its discrete intensity value and label index (from 1 to 180 for the left hemisphere, and from 1000 to 1180 for the right hemisphere). The Talairach atlas in /usr/local/fsl/data/atlases was used as a template .xml file and modified to create the file associated with the MMP1 atlas following the instructions found in the [FSLeyes documentation](https://users.fmrib.ox.ac.uk/~paulmc/fsleyes/userdoc/latest/customising.html#customising).

The ordered list of MMP1 labels was downloaded [here](https://figshare.com/articles/HCP-MMP1_0_projected_on_MNI2009a_GM_volumetric_in_NIfTI_format/3501911).

First, the atlas was reoriented to match the orientation that it's best-liked by FSLeyes with the command:

```
$ fslreorient2std MNI_Glasser_HCP_v1.0.nii.gz MNI_Glasser_HCP_v1.0.nii.gz
```

Then the following command was used to get the center of gravity of each label (the -K pre-option allows to treat them as separate sub-masks):

```
$ fslstats -K MNI_Glasser_HCP_v1.0.nii.gz MNI_Glasser_HCP_v1.0.nii.gz -C >> HCP-MMP1_COG.txt
```

These values were used to replace the list of labels and coordinates from the example template "Talairach.xml" file.

### Importing the atlas in FSL

- To download the HCP-MMP1 atlas without installing AFNI download the [latest atlas repository](https://afni.nimh.nih.gov/pub/dist/tgz/atlases_latest.tgz) of the package. Unzip the folder and copy the file "MNI_Glasser_HCP_v1.0.nii.gz" in your /downloads_path/. Reorient the file to match the orientation that it's best-liked by FSLeyes with the fslreorient2std command:
```
$ fslreorient2std /your_downloads_path/MNI_Glasser_HCP_v1.0.nii.gz /your_downloads_path/MNI_Glasser_HCP_v1.0.nii.gz
```
- (optional step) Download the MNI152 template from [AFNI](https://afni.nimh.nih.gov/pub/dist/tgz/suma_MNI152_2009.tgz). Unzip it and copy the file "T1.nii" where you prefer. This is the structural image to which the MMP1 atlas was mapped onto, and should correspond to the ICBM 2009c nonlinear asymmetric template.

- Download the file "HCP-Multi-Modal-Parcellation-1.0.xml" from this repository.

- Finally, copy the downloaded files in your FSL directory. For example, in a bash terminal, type (prefix all the commands with sudo if you don't have write permission, or switch to root with 'sudo -s'):

```
$ mkdir /usr/local/fsl/data/atlases/HCP-MMP1
$ cp /your_downloads_path/MNI_Glasser_HCP_v1.0.nii.gz /usr/local/fsl/data/atlases/HCP-MMP1/
$ cp /your_downloads_path/HCP-Multi-Modal-Parcellation-1.0.xml /usr/local/fsl/data/atlases/
```
### Using the atlas in FSL and FSLeyes

In FSLeyes you can now either load the file, or click on Settings - Orto View 1 - Atlas panel (shortcut Ctrl+Alt+6) to open the atlas panel window and tick the corresponding box ("HCP-MMP1") to explore your data.
In a bash terminal, type the following commands to query a nifti mask or a single coordinate, and to retrieve the MMP1 label that corresponds to it:

```
$ atlasq ohi -a HCP-Multi-Modal-Parcellation-1.0 -m your_mask.nii.gz
$ atlasq ohi -a HCP-Multi-Modal-Parcellation-1.0 -c X,Y,Z
```

That's it! I hope that this is of any use to you.
For questions and suggestions on how to improve this project please send an email to marco.bedini@unitn.it.

Author and affiliation:
Marco Bedini,
Ph.D. student,
Center for Mind/Brain Sciences,
University of Trento.

#### References
- https://www.nature.com/articles/nature18933
- https://openwetware.org/wiki/Beauchamp:CorticalSurfaceHCP
- https://www.pnas.org/content/115/27/E6356.short
