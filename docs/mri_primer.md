# MRI primer

The aim of this short section is to walk you through the basics of working with MRI data.

## PACS

**Picture Archiving and Communication System (PACS)** is a system that facilitates the transfer of digital images and personal data throughout a healthcare enterprise or a research institution. PACS are most commonly used to process images from diagnostic imaging modalities, such as MRI, CT, PET systems. The images are transmitted digitally from the modality and stored in PACS. The universal format for this is `DICOM` (Digital Imaging and Communications in Medicine).

Typically a PACS network consists of: a central **PACS server** which stores a database containing the images, as well as of multiple **PACS clients**. The imaging modalities send the images to the PACS Server. The PACS clients, typically equipped with a DICOM viewer (e.g. OsiriX, Horos), communicate with the PACS server to retrieve and display medical imaging data.

## OsiriX and Horos

[OsiriX](https://www.osirix-viewer.com) and [Horos](https://horosproject.org) are DICOM image viewers / browsers. Both viewers currently run on MacOS and/or OS X only.

**OsiriX** is one of the most widely used DICOM viewers. It comes in two versions:
* [OsiriX MD](https://www.osirix-viewer.com/osirix/osirix-md/), a commercial, full-featured version, certified for medical data use.
* [OsiriX Lite](https://www.osirix-viewer.com/osirix/osirix-md/download-osirix-lite/), a free demo version.

**Horos** is a free, open source, fully functional medical image viewer. Horos is based upon OsiriX and other open source medical imaging software. You can download it [here](https://horosproject.org/download-horos/).

## XNAT

**XNAT** is an open-source informatics platform dedicated for research data management in medical imaging research. XNAT's core functions are: importing, archiving, processing, and securely distributing imaging and related study data.

A nice, video tutorial that briefly introduces XNAT's basics can be found [here](https://www.youtube.com/watch?v=xROpMwC6WX4).

The official, extensive documentation is [here](https://wiki.xnat.org/documentation).

# Access MRI data

## Log into XNAT

Currently, the only recommended way of accessing your DICOM data is through XNAT. LOBI's XNAT instance is accessible through Nencki's local network only (either onsite or via VPN) and can be reached at: [http://pacs.nencki.gov.pl](http://pacs.nencki.gov.pl).

If you collect (or have collected) your MRI data, you likely have an XNAT account. Should you have any difficulties logging in, please contact [Bartek Kossowski](https://lobi.nencki.gov.pl/team/1/) who is the person responsible for maintaining our XNAT.

## Find your project

Once you're logged into XNAT, you should be able to access all research projects that you've been assigned to (either as _owner_ or as _collaborator_).

If you're unsure about your project name, the easiest way of finding it is to browse all existing projects using the top navigation (**Browse > All Projects**).

## Download your data

Depending on how many sessions you are downloading, or how granular you want to get concerning which scan or resource files to download, you can choose to download your data in different ways:

* If you want to download all your project's data at once, you can go to the project page and select **Download Images** from the actions menu.

<video width="640" height="480" controls>
  <source src="_static/xnat-download-fantomy.mov" type="video/mp4">
</video>

* If you're interested in any individual session, you can go to this session's page and select **Download > Download Images** from the actions menu.

<video width="640" height="480" controls>
  <source src="_static/xnat-download-poduszkagrubas.mov" type="video/mp4">
</video>

* If you're interested in a subset of data from your project, you can use the [advanced search](https://wiki.xnat.org/documentation/how-to-use-xnat/using-the-standard-search/using-the-advanced-search) and select **Options > Download** in the resulting table listing's menu.

<video width="640" height="480" controls>
  <source src="_static/xnat-download-advanced-search.mov" type="video/mp4">
</video>

**NOTE:** XNAT provides [extensive documentation](https://wiki.xnat.org/documentation/how-to-use-xnat/how-to-download-image-data-from-xnat-projects) on how to download your data.

# Work with MRI data

## Convert to NIfTI

There are many tools that can help you convert your images from `DICOM` (format used by scanner manufacturers) to `NIfTI` (format used by most analysis software). Here we only mention few of them.

If you prefer GUI-based software, you can use [MRIConvert](https://lcni.uoregon.edu/downloads/mriconvert/mriconvert-and-mcverter). Detailed instructions on how to install and use this software are provided [here](https://lcni.uoregon.edu/downloads/mriconvert/mriconvert-and-mcverter).

If you're more into command line, you can try `dcm2niix` (or `dcm2nii`), for more information go [here](https://www.nitrc.org/projects/dcm2nii/). Note, that this software is distributed with [MRIcron](https://www.nitrc.org/projects/mricron/) / [MRIcroGL](https://www.nitrc.org/projects/mricrogl/) image viewers and can be run from the command line or from the graphical user interface (GUI).
Alternatively, standalone versions (command line only) can be obtained directly from [GitHub](https://github.com/rordenlab/dcm2niix) or some package managers such as Homebrew (see the Install section in README of the linked GitHub page).

**NOTE:** Chris Rorden's Lab (group responsible for the development of `dcm2niix`) curates the [list](https://github.com/rordenlab/dcm2niix#alternatives) of software that can be used for `DICOM` to `NIfTI` conversion.

## Convert to BIDS

The **Brain Imaging Data Structure (BIDS)** is a community-developed standard for organizing data collected during neuroimaging experiments. The BIDS format is essentially a way to structure your data / metadata within a hierarchy of folders. The standard makes it easier for scientists to organize their own data and work with the data collected by others, facilitating collaboration and reproducibility efforts. A growing number of analysis software (see [BIDS Apps](http://bids-apps.neuroimaging.io/apps/)) require that a dataset is BIDS-compliant.

While originally specified for MRI data, BIDS has been already extended to several other imaging modalities (e.g. EEG, MEG).

For more information visit [BIDS](https://bids.neuroimaging.io/index.html) website.

A community-curated collection of tutorials, wikis, and templates to get you started with creating BIDS compliant datasets can be found [here](https://bids-standard.github.io/bids-starter-kit/index.html).

One converter worth mentioning is [HeuDiConv](https://heudiconv.readthedocs.io), which performs DICOM to NIfTI conversion with `dcm2niix` and generates file naming patterns based on a heuristic provided by the user.

Last, but certainly not least, there is a decent [Horos / OsiriX BIDS plugin](https://github.com/mslw/horos-bids-output), created by [Michał Szczepanik](https://github.com/mslw)!
