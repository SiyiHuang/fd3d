FD3D
====
FD3D is a companion program of [MaxwellFDFD](https://github.com/wsshin/maxwellfdfd).  It allows the users of MaxwellFDFD to solve the frequency-domain Maxwell's equations in a large 3D domain.  FD3D uses iterative methods to avoid the large memory requirement of direct methods.

See `INSTALL.md` for installation instruction.

Directory structure
-------------------
The FD3D root directory consists of the following subdirectories:
- `bin/`

	This directory will contain executables of the FD3D program and other utilities once they are built from the source codes in src/.  The main executable is `fd3d`.  To use these binaries, the path to this directory should be added to user's PATH evironment variable as instructed in INSTALL.

- `batchjob/`

	This directory contains an example batch job script that is used in typical LINUX clusters.

- `src/`

	This directory contains the source codes and makefiles to build executables in bin/.

- `test/`

	This directory contains test input files to verify successful installation of FD3D.


Workflow
--------
The general workflow to simulate EM wave propagation is as follows.

1. Create a project directory.

2. Create input files from MaxwellFDFD and store them in the project directory.

	Input files describe the EM media and wave sources in the simulation domain. You need four input files, which are typically named as `INPUT_NAME.h5`, `INPUT_NAME.eps.gz`, `INPUT_NAME.srcJ.gz`, `INPUT_NAME.srcM.gz`.  The input files are generated by MaxwellFDFD, a companion MATLAB package.  For the usage of MaxwellFDFD, consult its documentation. 

	If you run MaxwellFDFD (a MATLAB package) and FD3D on the same machine, you can create a MaxwellFDFD script in the same project directory.  Running the MaxwellFDFD script in MATLAB will create the input files for FD3D in the project directory.

	If you run MaxwellFDFD and FD3D on different machines (e.g., MaxwellFDFD on your laptop and FD3D on a LINUX cluster), then you need to upload the input files created by MaxwellFDFD to the project directory on the machine where FD3D runs.

3. Extract gzipped input files.

	This can be done by:

		gzip -d INPUT_NAME.{eps,srcJ,srcM}.gz

	When creating input files, MaxwellFDFD gzips those files (except for the small file `INPUT_NAME.h5`) by default for convenience, because you typically need to upload them to an external LINUX cluster.

4. Run `fd3d`.  

	For a single processor, execute:

		fd3d -i INPUT_NAME

	For multiple processors, execute something like:

		mpirun -n N fd3d -i INPUT_NAME

	where `INPUT_NAME` is the base name of the input files, and `N` is the number of processors.  The command for executing a program on multiple processors may not be `mpirun` on you system; for example, it can be `mpirun_rsh` or `mpiexec`.  See the user guide of your system to find out the exact command.

	Instead of running `fd3d` interactively, you can submit a batch job script if your system manages a job queue, e.g. PBS/TORQUE or SLURM.  Refer to the user guide of your system for more detail about writing a batch job script.  Also, see the example batch job script in `$FD3D_ROOT/batchjob/` direrctory.

5. Check the standard output.

	You should check stdout generated by `fd3d` to see if `fd3d` has run successfully.

6. Examine the solution.

	The solution of the frequency-domain Maxwell's equations is stored in `INPUT_NAME.E.h5` and `INPUT_NAME.H.h5` files, which contain the E- and H-field of the solution, respectively.  The solution in these files can be visualized in MaxwellFDFD.  See the MaxwellFDFD documentation for more details.
