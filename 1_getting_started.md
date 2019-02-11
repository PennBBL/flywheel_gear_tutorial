# Getting started with gear creation

## Prerequisites

You should develop gears on a machine where you have access to Docker and
the `fw` commandline tool.

## Start from scratch

In a directory where you want to create a gear, use the `fw` command to
generate some boilerplate. Here is an example run-through:

```
$ fw gear create
Checking Flywheel connectivity...

Welcome! First, give your gear a friendly, human-readable name.
✔ Name your gear: My Test Gear

Next, specify a gear ID. This is a unique, machine-friendly abbreviation.
✔ Name your gear: testgear123

Choose a Docker image to start your project.
Use the arrow keys to navigate: ↓ ↑ → ←
? Select base image:
+ ▸ Other
    python:3
    python:2
    ubuntu
```

If you have already been developing your software using Docker and the
image is available on your local machine, you can select `other` and type
the Docker tag. For example, we develop the `qsiprep` software locally
so we already have a Docker image cached. The rest of the prompts would
look like this:

```
Choose a Docker image to start your project.
✔ Other: pennbbl/qsiprep:latest

Is this a converter or an analysis gear?
✔ analysis

Checking if pennbbl/qsiprep:latest is available locally...
	Found tag locally.
Inspecting pennbbl/qsiprep:latest ...
	Image is the correct architecture.
Creating container from pennbbl/qsiprep:latest ...
	Created d5411e05d9c5
Checking if image has gear contents...
	No gear contents. Providing a starter kit...
Creating container from pennbbl/qsiprep:latest ...
	Created 39e560c1c352
Removing container 39e560c1c352 ...
	Removed container
✔ File manifest.json exists and will be replaced. Continue?: y
Removing container d5411e05d9c5 ...
	Removed container

Your gear is created and expanded to the current directory.
Try `fw gear local` to run the gear!
```

If you don't already have a Docker image available, you can choose one of the
other available images or an image from dockerhub.

## Examining the boilerplate

From the above command, we can see two files have been created in the
working directory, `example.sh` and `manifest.json`. The contents for
`manifest.json` are

```
{
	"name": "testgear123",
	"label": "My Test Gear",
	"description": "Created by the gear builder. Edit the manifest file to give this gear a description!",
	"version": "0",
	"inputs": {
		"api-key": {
			"base": "api-key"
		},
		"dicom": {
			"base": "file",
			"description": "Any dicom file.",
			"optional": true,
			"type": {
				"enum": [
					"dicom"
				]
			}
		}
	},
	"config": {
		"address": {
			"default": "Example",
			"description": "String example: any text.",
			"type": "string"
		},
		"age": {
			"default": 7,
			"description": "Integer example: any whole number.",
			"type": "integer"
		},
		"cost": {
			"default": 3.5,
			"description": "Float example: any real number.",
			"type": "number"
		},
		"fast": {
			"default": false,
			"description": "Boolean example: a toggle.",
			"type": "boolean"
		},
		"nickname": {
			"default": "Jimmy",
			"description": "String length example: 2 to 15 characters long.",
			"maxLength": 15,
			"minLength": 2,
			"type": "string"
		},
		"phone": {
			"default": "555-5555",
			"description": "String regex example: any phone number, no area code.",
			"pattern": "^[0-9]{3}-[0-9]{4}$",
			"type": "string"
		}
	},
	"environment": {
		"AFNI_IMSAVE_WARNINGS": "NO",
		"AFNI_INSTALLDIR": "/usr/lib/afni",
		"AFNI_MODELPATH": "/usr/lib/afni/models",
		"AFNI_PLUGINPATH": "/usr/lib/afni/plugins",
		"AFNI_TTATLAS_DATASET": "/usr/share/afni/atlases",
		"ANTSPATH": "/opt/ants-latest/bin",
		"C3DPATH": "/opt/convert3d-nightly",
		"CRN_SHARED_DATA": "/niworkflows_data",
		"FMRI_ANALYSIS_DIR": "/opt/freesurfer/fsfast",
		"FREESURFER_HOME": "/opt/freesurfer",
		"FSFAST_HOME": "/opt/freesurfer/fsfast",
		"FSLDIR": "/opt/fsl-5.0.11",
		"FSLOUTPUTTYPE": "NIFTI_GZ",
		"FUNCTIONALS_DIR": "/opt/freesurfer/sessions",
		"LANG": "C.UTF-8",
		"LC_ALL": "C.UTF-8",
		"LD_LIBRARY_PATH": "/opt/ants-latest/lib:",
		"LOCAL_DIR": "/opt/freesurfer/local",
		"MINC_BIN_DIR": "/opt/freesurfer/mni/bin",
		"MINC_LIB_DIR": "/opt/freesurfer/mni/lib",
		"MKL_NUM_THREADS": "1",
		"MNI_DATAPATH": "/opt/freesurfer/mni/data",
		"MNI_DIR": "/opt/freesurfer/mni",
		"MNI_PERL5LIB": "/opt/freesurfer/mni/lib/perl5/5.8.5",
		"OMP_NUM_THREADS": "1",
		"PATH": "/usr/local/miniconda/bin:/opt/convert3d-nightly/bin:/opt/ants-latest/bin:/opt/mrtrix3-latest/bin:/opt/freesurfer/bin:/opt/freesurfer/fsfast/bin:/opt/freesurfer/tktools:/opt/freesurfer/mni/bin:/opt/fsl-5.0.11/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/opt/dsi-studio/dsi_studio_64:/usr/lib/afni/bin",
		"PERL5LIB": "/opt/freesurfer/mni/lib/perl5/5.8.5",
		"PYTHONNOUSERSITE": "1",
		"QSIRECON_ATLAS": "/atlas/qsirecon_atlases",
		"SUBJECTS_DIR": "/opt/freesurfer/subjects"
	},
	"command": "./example.py",
	"author": "Matthew Cieslak",
	"maintainer": "Matthew Cieslak",
	"cite": "List citations here.",
	"license": "Other",
	"source": "",
	"url": "",
	"custom": {
		"gear-builder": {
			"category": "analysis",
			"image": "pennbbl/qsiprep:latest"
		}
	}
}
```

## Contents of `manifest.json`

Always use the most up-to-date version of the `fw` program to generate
boilerplate files. The spec seems to be frequently updated and the documentation
doesn't necessarily keep up. Here are some of the important items in the
`manifest.json` file.

### `config`
This section describes the parameters for your algorithm. These will be displayed
in the flywheel gui when the user chooses to run the gear. If you are
familiar with `nipype`, this is would be comparable to the traits
of the `input_spec` that get defined in your script. The nipype `input_spec`
traits that get defined during the execution of the workflow are specified in
`manifest.json`'s `inputs` item.

### `inputs`
These are the data items that will be inputs to your algorithm. These
are explained well in the flywheel [documentation](https://github.com/flywheel-io/gears/tree/master/spec#manifest-inputs).

### `environment`
These values are pulled from the Dockerfile by default, but additional
values can be added.

### `custom`
Since the spec for `manifest.json` files changes all the time, this section
contains metadata about the gear itself. by default it should have a
`gear-builder` item that contains a `category` item (set to either `"analysis"`
or `"converter"`) and an `image` item that contains the full tag of the
docker image used.

### `command`
This field is optional. It can contain the command that will be executed
when the gear is run. An alternative is to include a bash script called
`run` and excluding this item.

### `license`
This should be the license associated with the software wrapped in the
gear, NOT the license of the gear itself.
