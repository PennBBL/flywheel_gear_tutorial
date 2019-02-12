# How to access data

## Simplest case
The data specified in the `inputs` item in your `manifest.json` is automatically
mounted in `/flywheel/v0/inputs`. The official documentation for input
data mounting can be found [here](https://github.com/flywheel-io/gears/tree/master/spec#the-input-folder).

Many of the simplest gears operate this way. For example, the BET gear
only needs a T1w image. The input/output system for gears only handles single
files. These files can be the zipped outputs from an analysis gear. This is
straightforward for many cases, but accessing BIDS data is a special case.

The data is never actually stored in BIDS format on flywheel unless it was
originally uploaded after BIDS conversion. This means that the flywheel API
has to be used to query for the BIDS names of the data, then the data is downloaded
to the `/flywheel/v0/inputs` directory and renamed to be BIDS-compliant.

## Getting the BIDS-formatted data

There is a [large collection of applications](https://bids-apps.neuroimaging.io/)
that are already containerized and designed to run on BIDS-formatted data.
These are great candidates to be turned into gears, but the flywheel system
can only mount single files using the `inputs` specification in `manifest.json`.
So how does one get their flywheel data in BIDS format as input to a gear?

This is not clear in their documentation, but the following is taken from their
FMRIPREP gear.

The [create_archive_funcs.py](https://github.com/PennBBL/fmriprep/blob/master/create_archive_funcs.py) script has a main function `get_flywheel_hierarchy`.
This uses the flywheel API to get a dictionary of the contents of either
the subject or project that the gear is supposed to run on.
