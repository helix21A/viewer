# LDPM Workbench Documentation

The LDPM Workbench is an open-source preprocessing environment for generating discrete-particle and lattice-based computational models. It provides panels for geometry, particle distributions, mixture composition, reinforcement, random fields, mesh preview, model generation, and solver-input preparation.

## Contents

- [Prerequisites](#prerequisites)
- [LDPM Generation](#ldpm-generation)
  - [Model Settings](#ldpm-model-settings)
  - [Geometry](#ldpm-geometry)
  - [Particles](#ldpm-particles)
  - [Mix Design](#ldpm-mix-design)
  - [M-LDPM Parameters](#m-ldpm-parameters)
  - [Periodic Mesh Parameters](#periodic-mesh-parameters)
  - [Reinforcement Parameters](#ldpm-reinforcement-parameters)
  - [Random Field](#ldpm-random-field)
  - [Mesh Preview](#ldpm-mesh-preview)
  - [Model Generation](#ldpm-model-generation)
  - [Generate Single Tet](#ldpm-generate-single-tet)
- [P-LDPM Generation](#pldpm-generation)
  - [Model Settings](#pldpm-model-settings)
  - [Geometry](#pldpm-geometry)
  - [Particles](#pldpm-particles)
  - [Mix Design](#pldpm-mix-design)
  - [Multimaterial Parameters](#multimaterial-parameters)
  - [Mesh Preview](#pldpm-mesh-preview)
  - [Model Generation](#pldpm-model-generation)
  - [Generate Single Tet](#pldpm-generate-single-tet)
- [3DCP-LDPM Generation](#3dcp-ldpm-generation)
  - [Model Settings](#3dcp-model-settings)
  - [Geometry](#3dcp-geometry)
  - [Particles](#3dcp-particles)
  - [Mix Design](#3dcp-mix-design)
  - [Reinforcement Parameters](#3dcp-reinforcement-parameters)
  - [Additional Reinforcement Parameters](#additional-reinforcement-parameters)
  - [Interlayer Parameters](#interlayer-parameters)
  - [Mesh Preview](#3dcp-mesh-preview)
  - [Model Generation](#3dcp-model-generation)
  - [Generate Single Tet](#3dcp-generate-single-tet)
- [SPH/DEM Generation](#sphdem-generation)
  - [Model Settings](#sphdem-model-settings)
  - [Geometry](#sphdem-geometry)
  - [Particles](#sphdem-particles)
  - [Mix Design](#sphdem-mix-design)
  - [Model Generation](#sphdem-model-generation)
- [RF-Generation](#rf-generation)
  - [Model Settings](#rf-model-settings)
  - [Random Field Parameters](#random-field-parameters)
  - [Random Field Generation](#random-field-generation)
- [LDPM Simulation Inputs](#ldpm-simulation-inputs)
  - [Import Mesh Bundle](#import-mesh-bundle)
  - [Confirm Output](#confirm-output)
  - [Write File](#write-file)
- [Standalone Mesh Preview](#standalone-mesh-preview)
  - [Mesh Preview](#standalone-mesh-preview-panel)

---

## <a id="prerequisites"></a>Prerequisites

MPI and TetGen requirements

The external generation workflow requires `numpy`, `mpi4py`, an MPI runtime that provides `mpiexec`, and the TetGen executable. Install the MPI runtime before installing `mpi4py`, and install the Python packages in the same Python environment used to run the workbench.

On Windows, install the Microsoft MPI runtime and SDK, then place `TetGen.exe` in a directory listed in `PATH`. On Linux, install MPICH, its development package, and TetGen through the system package manager. If TetGen is not provided by the distribution, install the executable separately and add its directory to `PATH`.

```powershell
python -m pip install --upgrade pip
python -m pip install numpy mpi4py
```

For a Debian- or Ubuntu-based Linux system, the runtime prerequisites can be installed with:

```bash
sudo apt-get update
sudo apt-get install -y mpich libmpich-dev tetgen
```

Verify the Python packages, MPI runtime, and process launch:

```powershell
python -c "import numpy; from mpi4py import MPI; print(numpy.__version__); print(MPI.Get_version())"
mpiexec -help
mpiexec -n 2 python -c "from mpi4py import MPI; print(MPI.COMM_WORLD.Get_rank())"
tetgen -h
```

The directory containing `mpiexec` must be present in the system `PATH`. Confirm that TetGen can also be launched from the same environment before using external generation.

---

## <a id="ldpm-generation"></a>LDPM Generation

### <a id="ldpm-model-settings"></a>Model Settings

Model Settings

Contents: [Read Existing Model Setup File](#ldpm-read-setup) · [Cell Generation Parameters](#ldpm-cell-generation) · [MPI Parameters](#ldpm-mpi-parameters) · [Setup Sample Parameters](#ldpm-setup-samples)

#### <a id="ldpm-read-setup"></a>Read Existing Model Setup File

- `...`: Opens the model setup file browser and loads the selected parameter file.

#### <a id="ldpm-cell-generation"></a>Cell Generation Parameters

- `Number of CPUs`: Specifies the processor count used during cell and particle placement.
- `Number of Placement Increments`: Specifies the number of placement batches.
- `Maximum Placement Iterations`: Limits the placement attempts for each particle.
- `Placement Algorithm`: Selects `Trial-and-Error` placement.

`Number of Placement Increments` divides the ordered particle list into batches. `Number of CPUs` is the processor count associated with that placement. `Maximum Placement Iterations` is the limit for one particle. These three values are fixed in the panel. `Placement Algorithm` only uses`Trial-and-Error`.

#### <a id="ldpm-mpi-parameters"></a>MPI Parameters

- `MPI turn On/Off (External only)`: Selects `Off` or `On` for external MPI generation. MPI extrernal bundle only generates particles and fibers outside FreeCAD.
- `Rank`: Specifies the number of MPI processes used by the external workflow.
- `Number of Nodes`: Specifies the number of compute nodes.

`MPI turn On/Off (External only)` applies only to `External Model Generation`. `Off` runs the external package in one process. `On` uses `Rank` processes for particle placement and, when fibers are enabled, for fiber placement. Facet generation uses that count up to 4 processes due to memory issues. `Number of Nodes` is recorded with the package and is fixed at one.

#### <a id="ldpm-setup-samples"></a>Setup Sample Parameters

- `Number of Samples`: Specifies the number of model samples.

`Number of Samples` is fixed in the panel. When a random field is on, generation reads this value and uses the realization assigned to each sample.

---

### <a id="ldpm-geometry"></a>Geometry

Geometry

Contents: [Parameters](#ldpm-geometry-parameters)

#### <a id="ldpm-geometry-parameters"></a>Parameters

<ul style="list-style-type: disc;">
<li><code>Box</code>: Builds a rectangular solid.
<ul style="list-style-type: disc;">
<li><code>X:</code>: Sets the extent along X.</li>
<li><code>Y:</code>: Sets the extent along Y.</li>
<li><code>Z:</code>: Sets the extent along Z.</li>
</ul>
</li>
<li><code>Cylinder</code>: Builds a circular cylinder.
<ul style="list-style-type: disc;">
<li><code>Radius (along XY-Plane):</code>: Sets the radius in the horizontal plane.</li>
<li><code>Height (along Z-axis):</code>: Sets the height.</li>
</ul>
</li>
<li><code>Cone</code>: Builds a circular cone.
<ul style="list-style-type: disc;">
<li><code>Radius (along XY-Plane):</code>: Sets the base radius in the horizontal plane.</li>
<li><code>Height (along Z-axis):</code>: Sets the height.</li>
</ul>
</li>
<li><code>Sphere</code>: Builds a sphere.
<ul style="list-style-type: disc;">
<li><code>Radius:</code>: Sets the sphere radius.</li>
</ul>
</li>
<li><code>Ellipsoid</code>: Builds an ellipsoid.
<ul style="list-style-type: disc;">
<li><code>Radius 1:</code>: Sets the first principal radius.</li>
<li><code>Radius 2:</code>: Sets the second principal radius.</li>
<li><code>Radius 3:</code>: Sets the third principal radius.</li>
<li><code>U parameter:</code>: Sets the angular extent about the first axis.</li>
<li><code>V parameter:</code>: Sets the angular extent about the second axis.</li>
</ul>
</li>
<li><code>Arbitrary Prism</code>: Builds a regular prism.
<ul style="list-style-type: disc;">
<li><code>Polygon:</code>: Sets the number of sides.</li>
<li><code>Circumradius (along XY-Plane):</code>: Sets the radius of the circle through the corners.</li>
<li><code>Height (along Z-axis):</code>: Sets the height.</li>
</ul>
</li>
<li><code>Notched Prism - Square</code>: Builds a prism with a square notch.
<ul style="list-style-type: disc;">
<li><code>Length (along X-axis):</code>: Sets the length.</li>
<li><code>Width (along Y-axis):</code>: Sets the width.</li>
<li><code>Height (along Z-axis):</code>: Sets the height.</li>
<li><code>Notch Width (along X-axis):</code>: Sets the width of the square notch.</li>
<li><code>Notch Depth (along Z-axis):</code>: Sets the depth of the square notch.</li>
</ul>
</li>
<li><code>Notched Prism - Semi Circle</code>: Builds a prism with a semicircular notch.
<ul style="list-style-type: disc;">
<li><code>Length (along X-axis):</code>: Sets the length.</li>
<li><code>Width (along Y-axis):</code>: Sets the width.</li>
<li><code>Height (along Z-axis):</code>: Sets the height.</li>
<li><code>Notch Width (along X-axis):</code>: Sets the width of the semicircular notch.</li>
<li><code>Notch Depth (along Z-axis):</code>: Sets the depth of the semicircular notch.</li>
</ul>
</li>
<li><code>Notched Prism - Semi Ellipse</code>: Builds a prism with a semi-elliptical notch.
<ul style="list-style-type: disc;">
<li><code>Length (along X-axis):</code>: Sets the length.</li>
<li><code>Width (along Y-axis):</code>: Sets the width.</li>
<li><code>Height (along Z-axis):</code>: Sets the height.</li>
<li><code>Notch Width (along X-axis):</code>: Sets the width of the notch.</li>
<li><code>Notch Depth (along Z-axis):</code>: Sets the depth of the notch.</li>
<li><code>Notch Tip Depth (along Z-axis):</code>: Sets the depth of the elliptical tip.</li>
</ul>
</li>
<li><code>Dogbone</code>: Builds a dogbone specimen.
<ul style="list-style-type: disc;">
<li><code>Length (along Z-axis):</code>: Sets the overall length.</li>
<li><code>Width (along X-axis):</code>: Sets the overall width.</li>
<li><code>Thickness (along Y-axis):</code>: Sets the thickness.</li>
<li><code>Gauge Length (along Z-axis):</code>: Sets the length of the reduced section.</li>
<li><code>Gauge Width (along X-axis):</code>: Sets the width of the reduced section.</li>
<li><code>Dogbone Type:</code>: Selects the transition at the reduced section.
<ul style="list-style-type: disc;">
<li><code>Rounded</code>: Uses a rounded transition.</li>
<li><code>Straight</code>: Uses a straight transition.</li>
</ul>
</li>
</ul>
</li>
<li><code>Custom</code>: Uses a solid already present in the document.
<ul style="list-style-type: disc;">
<li><code>Select Object</code>: Assigns the selected document solid as the specimen.</li>
<li><code>Deselect Object</code>: Clears that assignment.</li>
</ul>
</li>
<li><code>Import CAD or Mesh</code>: Uses an external geometry file. The solid must be closed.
<ul style="list-style-type: disc;">
<li><code>...</code>: Opens the geometry-file browser.</li>
</ul>
</li>
</ul>

---

### <a id="ldpm-particles"></a>Particles

Particles

- `Minimum Particle Size in Simulation [mm]`: Sets the lower particle-size bound.
- `Maximum Particle Size in Simulation [mm]`: Sets the upper particle-size bound.
- `Particle Offset Parameter`: Controls the particle-placement offset.

The particle list is calculated from the specimen volume and the mix design. A Fuller coefficient or the entered sieve curve sets the aggregate volume and the diameter distribution. Diameters are retained only between the two simulation size limits and are ordered from largest to smallest. `Trial-and-Error` then places each particle inside the mesh. A trial point is drawn inside a tetrahedron and is accepted only when the particle lies inside the specimen and does not overlap a particle already placed. The offset keeps a clearance around each particle. A particle that cannot be placed within `Maximum Placement Iterations` stops the generation.

---

### <a id="ldpm-mix-design"></a>Mix Design

Mix Design

#### Concrete Mix Design

- `Minimum Aggregate Size [mm]` and `Maximum Aggregate Size [mm]`: Define the aggregate-size interval.
- `Fuller Coefficient`: Defines the aggregate gradation coefficient.
- `Sieve Curve Diameters "[ mm , ... ]"` and `Sieve Curve Passing "[  -  , ... ]"`: Define the sieve curve.
- `w/c Ratio`: Defines the water-to-cement ratio.
- `Cement Content [tonne/mm3 Concrete]`, `Fly Ash Content [tonne/mm3 Concrete]`, `Silica Fume Content [tonne/mm3 Concrete]`, and `Other SCMs Content [tonne/mm3 Concrete]`: Define binder contents.
- `Air Volume Fraction`: Defines the air fraction.
- `Density of Water [tonne/mm3]`, `Density of Cement [tonne/mm3]`, `Density of Fly Ash [tonne/mm3]`, `Density of Silica Fume [tonne/mm3]`, and `Density of Other SCMs [tonne/mm3]`: Define constituent densities.

Each content divided by its density is a volume fraction. Water volume follows `w/c Ratio` and the cement content. The aggregate volume fraction is what remains after cement, supplementary materials, water, and `Air Volume Fraction` are removed. A sieve curve replaces the Fuller distribution when both diameter and passing lists are entered. Otherwise `Fuller Coefficient` sets the size curve between the two aggregate-size limits.

---

### <a id="m-ldpm-parameters"></a>M-LDPM Parameters

M-LDPM Parameters

- `Turn On/Off M-LDPM`: Selects `Off` or `On` for the surface-case formulation.
- `Edge element length for surface cases`: Specifies the edge-element length in millimetres.

`On` adds edge elements along the specimen surface after the cells have been formed. `Edge element length for surface cases` is the length of those elements. `Off` leaves the model without surface edge elements.

---

### <a id="periodic-mesh-parameters"></a>Periodic Mesh Parameters

Periodic mesh parameters

- `Turn On/Off periodic meshing`: Selects `Off` or `On` for periodic meshing.

`On` builds a periodic surface from the current geometry and then meshes that surface. The particle and cell generation that follows uses this periodic surface. `Off` meshes the selected geometry directly.

---

### <a id="ldpm-reinforcement-parameters"></a>Reinforcement Parameters

Reinforcement Parameters

#### Fiber-Reinforcement Parameters

- `Turn On/Off fibers`: Enables or disables fibers.
- `Turn On/Off fiber cutting`: Enables or disables fiber cutting.
- `Diameter of fibers` and `Length of fibers`: Define fiber dimensions.
- `Fiber volume fraction`: Defines the fiber volume fraction.
- `Orientation`: Defines the orientation components.
- `Preferredness for orientation`: Defines the orientation preference.
- `File for fibers from CT scan`: Selects a fiber input file when available.
- `Perform fiber-facet intersection checks`: Enables or disables intersection checks.
- `Create Fiber Output Files`: Enables or disables fiber output files.
- `Fiber crossing at interface`: Enables or disables fiber crossing at interfaces.

When `Turn On/Off fibers` is `On`, the fiber count follows the specimen volume, `Fiber volume fraction`, `Diameter of fibers`, and `Length of fibers`. Each fiber begins at a random point inside the mesh. If `Orientation` is zero, the direction is random. Otherwise the direction follows the three orientation components, and `Preferredness for orientation` controls how tightly the fibers follow that direction: `0` is the widest scatter and `1` follows the prescribed direction. `Turn On/Off fiber cutting` shortens a fiber that would leave the specimen. `File for fibers from CT scan` replaces this generated set with measured fiber end points, directions, and lengths. `Perform fiber-facet intersection checks` records where fibers cross cell facets, and `Create Fiber Output Files` saves the fiber end points, directions, lengths, diameter, and count.

---

### <a id="ldpm-random-field"></a>Random Field

Random Field

- `Turn off/on Random Field`: Selects `Off` or `On`.
- `Upload Random Field Input Files`: Selects the random-field input folder.
- `Assign Realization`: Identifies the assignment area.
- `Sample Group`: Specifies the sample group.
- `Realizations`: Specifies the realization identifiers.
- `Submit`: Adds the selected assignment.

A random field describes spatially correlated variation of elasticity, strength, and fracture energy. Generate the field first with [RF-Generation](#rf-generation). That command writes a field folder containing the realizations, the grid, the correlation data required to evaluate the field, a generation report, and a copy of the parameter file. When visualization output is selected, the folder also contains a visualization of the field.

`Upload Random Field Input Files` selects that folder. `Sample Group` and `Realizations` name the model sample and the realization to use, and `Submit` records the pair. With `Turn off/on Random Field` set to `On`, generation evaluates the assigned realization at the facet locations and stores three multipliers with each facet: elasticity, strength, and fracture energy. The specimen must lie inside the random-field domain. One realization is used for each sample.

---

### <a id="ldpm-mesh-preview"></a>Mesh Preview

Mesh Preview

#### Preview

- `Generate Surface Mesh`: Generates the preview surface mesh.
- `Open in Viewer`: Opens the generated preview.

`Generate Surface Mesh` builds a surface from the current geometry so the specimen can be inspected before model generation. `Open in Viewer` opens that surface. It does not place particles or write the model files.

---

### <a id="ldpm-model-generation"></a>Model Generation

Model Generation

Contents: [Status](#ldpm-generation-status) · [Output Directory](#ldpm-output-directory) · [Generation](#ldpm-generation-controls)

#### <a id="ldpm-generation-status"></a>Status

- `Status`: Displays the current operation.
- `Completion %p%`: Displays generation progress.

#### <a id="ldpm-output-directory"></a>Output Directory

- `...`: Selects the output directory.

#### <a id="ldpm-generation-controls"></a>Generation

- `Model Type`: Selects `Lattice Discrete Particle Model (LDPM)` or `Confinement Shear Lattice (CSL) - LDPM Style` .
- `Generate Model Data Files`: Enables model data-file output.
- `Generate Model Visualization Files`: Enables model visualization-file output.
- `Generate Single Tetrahedron and Cell Visualization Files`: Enables single-tetrahedron and cell-visualization output.
- `Model Generation`: Builds the geometry, mesh, particle system, cells, and any enabled fibers inside the current session, then writes the selected data and visualization files.
- `External Model Generation`: Writes an external package and does not finish the mesh, particles, fibers, or facets in the current session.
- `Debug Model Generation`: Runs the accelerated path. It saves the mesh and then places particles, fibers, and facets in placement increments.
- `Write Parameters`: Writes the current panel values to `ldpmWorkbench.cwPar` in the output directory and does not generate a model.

`External Model Generation` creates a folder in the selected output directory. The folder contains `run_external_ldpmcsl.py`, a boundary representation of the geometry, and `external_manifest_ldpmcsl.json`. P-LDPM also copies its material and aggregate voxel files. 3DCP-LDPM also copies the sweep-path segments and, when additional reinforcement uses a drawing, an ASCII DXF saved as `reinforcement.dxf`.

Manifest generated by the external run. It stores the same input as `cwPar` . The MPI record uses `MPI turn On/Off (External only)`, `Rank`, and `Number of Nodes`. When MPI is `On`, particles and fibers use `Rank` processes, and facet generation uses that count up to four processes. When MPI is `Off`, the same sequence runs in one process.

Start the package from its folder:

```powershell
python run_external_ldpmcsl.py
```

---

### <a id="ldpm-generate-single-tet"></a>Generate Single Tet

Generate Single Tet

- `Generate Regular Tet`: Generates a regular tetrahedron for inspection.
- `Generate Irregular Tet`: Generates an irregular tetrahedron for inspection.

`Generate Regular Tet` writes one equilateral tetrahedron with four equal particle diameters. `Generate Irregular Tet` writes one skewed tetrahedron with four different diameters. Both are inspection models and do not use the specimen geometry.

---

## <a id="pldpm-generation"></a>P-LDPM Generation

### <a id="pldpm-model-settings"></a>Model Settings

Model Settings

Contents: [Read Existing Model Setup File](#pldpm-read-setup) · [Cell Generation Parameters](#pldpm-cell-generation) · [MPI Parameters](#pldpm-mpi-parameters) · [Setup Sample Parameters](#pldpm-setup-samples)

#### <a id="pldpm-read-setup"></a>Read Existing Model Setup File

- `...`: Opens the model setup file browser and loads the selected parameter file.

#### <a id="pldpm-cell-generation"></a>Cell Generation Parameters

- `Number of CPUs`: Specifies the processor count used during cell and particle placement.
- `Number of Placement Increments`: Specifies the number of placement batches.
- `Maximum Placement Iterations`: Limits the placement attempts for each particle.
- `Placement Algorithm`: Selects `Trial-and-Error` placement.

#### <a id="pldpm-mpi-parameters"></a>MPI Parameters

- `MPI turn On/Off (External only)`: Selects `Off` or `On` for external MPI generation.
- `Rank`: Specifies the number of MPI processes used by the external workflow.
- `Number of Nodes`: Specifies the number of compute nodes.

#### <a id="pldpm-setup-samples"></a>Setup Sample Parameters

- `Number of Samples`: Specifies the number of model samples.

Cell generation, MPI, and sample settings follow [LDPM Model Settings](#ldpm-model-settings).

---

### <a id="pldpm-geometry"></a>Geometry

Geometry

#### Parameters

<ul style="list-style-type: disc;">
<li><code>Box</code>: Builds a rectangular solid.
<ul style="list-style-type: disc;">
<li><code>X:</code>: Sets the extent along X.</li>
<li><code>Y:</code>: Sets the extent along Y.</li>
<li><code>Z:</code>: Sets the extent along Z.</li>
</ul>
</li>
<li><code>Cylinder</code>: Builds a circular cylinder.
<ul style="list-style-type: disc;">
<li><code>Radius (along XY-Plane):</code>: Sets the radius in the horizontal plane.</li>
<li><code>Height (along Z-axis):</code>: Sets the height.</li>
</ul>
</li>
<li><code>Cone</code>: Builds a circular cone.
<ul style="list-style-type: disc;">
<li><code>Radius (along XY-Plane):</code>: Sets the base radius in the horizontal plane.</li>
<li><code>Height (along Z-axis):</code>: Sets the height.</li>
</ul>
</li>
<li><code>Sphere</code>: Builds a sphere.
<ul style="list-style-type: disc;">
<li><code>Radius:</code>: Sets the sphere radius.</li>
</ul>
</li>
<li><code>Ellipsoid</code>: Builds an ellipsoid.
<ul style="list-style-type: disc;">
<li><code>Radius 1:</code>: Sets the first principal radius.</li>
<li><code>Radius 2:</code>: Sets the second principal radius.</li>
<li><code>Radius 3:</code>: Sets the third principal radius.</li>
<li><code>U parameter:</code>: Sets the angular extent about the first axis.</li>
<li><code>V parameter:</code>: Sets the angular extent about the second axis.</li>
</ul>
</li>
<li><code>Arbitrary Prism</code>: Builds a regular prism.
<ul style="list-style-type: disc;">
<li><code>Polygon:</code>: Sets the number of sides.</li>
<li><code>Circumradius (along XY-Plane):</code>: Sets the radius of the circle through the corners.</li>
<li><code>Height (along Z-axis):</code>: Sets the height.</li>
</ul>
</li>
<li><code>Notched Prism - Square</code>: Builds a prism with a square notch.
<ul style="list-style-type: disc;">
<li><code>Length (along X-axis):</code>: Sets the length.</li>
<li><code>Width (along Y-axis):</code>: Sets the width.</li>
<li><code>Height (along Z-axis):</code>: Sets the height.</li>
<li><code>Notch Width (along X-axis):</code>: Sets the width of the square notch.</li>
<li><code>Notch Depth (along Z-axis):</code>: Sets the depth of the square notch.</li>
</ul>
</li>
<li><code>Notched Prism - Semi Circle</code>: Builds a prism with a semicircular notch.
<ul style="list-style-type: disc;">
<li><code>Length (along X-axis):</code>: Sets the length.</li>
<li><code>Width (along Y-axis):</code>: Sets the width.</li>
<li><code>Height (along Z-axis):</code>: Sets the height.</li>
<li><code>Notch Width (along X-axis):</code>: Sets the width of the semicircular notch.</li>
<li><code>Notch Depth (along Z-axis):</code>: Sets the depth of the semicircular notch.</li>
</ul>
</li>
<li><code>Notched Prism - Semi Ellipse</code>: Builds a prism with a semi-elliptical notch.
<ul style="list-style-type: disc;">
<li><code>Length (along X-axis):</code>: Sets the length.</li>
<li><code>Width (along Y-axis):</code>: Sets the width.</li>
<li><code>Height (along Z-axis):</code>: Sets the height.</li>
<li><code>Notch Width (along X-axis):</code>: Sets the width of the notch.</li>
<li><code>Notch Depth (along Z-axis):</code>: Sets the depth of the notch.</li>
<li><code>Notch Tip Depth (along Z-axis):</code>: Sets the depth of the elliptical tip.</li>
</ul>
</li>
<li><code>Dogbone</code>: Builds a dogbone specimen.
<ul style="list-style-type: disc;">
<li><code>Length (along Z-axis):</code>: Sets the overall length.</li>
<li><code>Width (along X-axis):</code>: Sets the overall width.</li>
<li><code>Thickness (along Y-axis):</code>: Sets the thickness.</li>
<li><code>Gauge Length (along Z-axis):</code>: Sets the length of the reduced section.</li>
<li><code>Gauge Width (along X-axis):</code>: Sets the width of the reduced section.</li>
<li><code>Dogbone Type:</code>: Selects the transition at the reduced section.
<ul style="list-style-type: disc;">
<li><code>Rounded</code>: Uses a rounded transition.</li>
<li><code>Straight</code>: Uses a straight transition.</li>
</ul>
</li>
</ul>
</li>
<li><code>Custom</code>: Uses a solid already present in the document.
<ul style="list-style-type: disc;">
<li><code>Select Object</code>: Assigns the selected document solid as the specimen.</li>
<li><code>Deselect Object</code>: Clears that assignment.</li>
</ul>
</li>
<li><code>Import CAD or Mesh</code>: Uses an external geometry file. The solid must be closed.
<ul style="list-style-type: disc;">
<li><code>...</code>: Opens the geometry-file browser.</li>
</ul>
</li>
</ul>

---

### <a id="pldpm-particles"></a>Particles

Particles

- `Particles for Multimaterial Phases`: Selects `Select Phases`, `2-Phase`, or `3-Phase`.
- `Phase 1`, `Phase 2`, and `Phase 3`: Present the particle settings of the corresponding phase. `Phase 1` and `Phase 2` are presented for `2-Phase`. `Phase 3` is presented for `3-Phase`.
- `Minimum Particle Size in Simulation [mm]`: Sets the lower diameter retained for that phase.
- `Maximum Particle Size in Simulation [mm]`: Sets the upper diameter retained for that phase.
- `Particle Offset Parameter`: Sets the clearance kept around each particle of that phase, in proportion to the particle diameter.

The particle volume of a phase is the complement of its `Filler Volume Fraction (Vf)` in [Mix Design](#pldpm-mix-design), applied to the portion of the specimen assigned to that phase by the material voxels. Diameters inside that volume follow the grain limits and the Fuller coefficient or sieve curve of the same phase in [Multimaterial Parameters](#multimaterial-parameters). They are ordered from largest to smallest. Placement follows the trial-and-error rule in [LDPM Particles](#ldpm-particles), and a particle is accepted only inside the voxels of its phase.

---



### <a id="pldpm-mix-design"></a>Mix Design

#### Arbitrary Mix Design

- `Phases`: Selects `Select Phases`, `2-Phase`, or `3-Phase`. The selection follows `Particles for Multimaterial Phases` on the Particles panel.
- `Phase 1`: Specifies `Filler Volume Fraction (Vf)` for the first phase. The block is presented for `2-Phase` and `3-Phase`.
- `Phase 2`: Specifies `Filler Volume Fraction (Vf)` for the second phase. The block is presented for `2-Phase` and `3-Phase`.
- `Phase 3`: Specifies `Filler Volume Fraction (Vf)` for the third phase. The block is presented for `3-Phase`.

`Filler Volume Fraction (Vf)` is the volume fraction of the non-particle constituent in that phase. The particle volume fraction is the complement of this value. The phase volume is the specimen volume multiplied by the share of material voxels assigned to the phase, and the particle volume is that phase volume multiplied by one minus `Filler Volume Fraction (Vf)`. The diameters that fill this particle volume are taken from the grain limits and the Fuller coefficient or sieve curve of the same phase in [Multimaterial Parameters](#multimaterial-parameters).

---

### <a id="multimaterial-parameters"></a>Multimaterial Parameters

Multimaterial Parameters

#### Multimaterial Parameter

- `Turn On/Off multi-material meshing`: Selects `Off` or `On`.
- `Voxel file for defining materials`: Selects the material-assignment voxel file.
- `Voxel file for defining aggregate`: Selects the aggregate voxel file.
- `Rule for multi-material assignments`: Specifies the rule applied when a cell meets more than one material.

When multi-material meshing is `On`, each phase is generated from its own voxels. `Voxel file for defining materials` assigns a material to each point of the specimen. `Voxel file for defining aggregate` identifies the aggregate voxels.

#### Multimaterial Phases

- `Phase 1 - Aggregate`: Specifies `Agg Min Grain`, `Max`, `Agg Fuller Coefficient`, `Agg Sieve Diameters [mm]`, and `Agg Sieve Passing` for the aggregate phase.
- `Phase 2 - ITZ`: Specifies `ITZ Min Grain`, `Max`, `ITZ Fuller Coefficient`, `ITZ Sieve Diameters [mm]`, and `ITZ Sieve Passing` for the interfacial transition zone. This block is presented for `3-Phase`.
- `Phase 2 - Mortar/Binder`: Specifies `Binder Min Grain`, `Max`, `Binder Fuller Coefficient`, `Binder Sieve Diameters`, and `Binder Sieve Passing` for the mortar or binder when `2-Phase` is selected.
- `Phase 3 - Mortar/Binder`: Specifies the same binder fields when `3-Phase` is selected. The binder block receives this title in that case.

`2-Phase` presents `Phase 1 - Aggregate` and `Phase 2 - Mortar/Binder`. `3-Phase` also presents `Phase 2 - ITZ`, and the binder block is titled `Phase 3 - Mortar/Binder`. The grain limits, Fuller coefficient, and sieve lists of a phase determine the diameters generated in that phase. The particle volume of the phase is set by `Filler Volume Fraction (Vf)` in [Mix Design](#pldpm-mix-design).

---

### <a id="pldpm-mesh-preview"></a>Mesh Preview

Mesh Preview

#### Preview

- `Generate Surface Mesh`: Generates the preview surface mesh.
- `Open in Viewer`: Opens the generated preview.

The preview follows [LDPM Mesh Preview](#ldpm-mesh-preview).

---

### <a id="pldpm-model-generation"></a>Model Generation

Model Generation

Contents: [Status](#pldpm-generation-status) · [Output Directory](#pldpm-output-directory) · [Generation](#pldpm-generation-controls)

#### <a id="pldpm-generation-status"></a>Status

- `Status`: Displays the current operation.
- `Completion %p%`: Displays generation progress.

#### <a id="pldpm-output-directory"></a>Output Directory

- `...`: Selects the output directory.

#### <a id="pldpm-generation-controls"></a>Generation

- `Model Type`: Selects `Lattice Discrete Particle Model (LDPM)` or `Confinement Shear Lattice (CSL) - LDPM Style` .
- `Generate Model Data Files`: Enables model data-file output.
- `Generate Model Visualization Files`: Enables model visualization-file output.
- `Generate Single Tetrahedron and Cell Visualization Files`: Enables single-tetrahedron and cell-visualization output.
- `Model Generation`: Builds the geometry, mesh, phase particle systems, and cells inside the current session, then writes the selected files.
- `External Model Generation`: Writes the external package described in [LDPM Model Generation](#ldpm-model-generation), including the material and aggregate voxel files.
- `Debug Model Generation`: Saves the mesh and then places the phase particles and facets in placement increments.
- `Write Parameters`: Writes the current panel values to `ldpmWorkbench.cwPar` and does not generate a model.

---

### <a id="pldpm-generate-single-tet"></a>Generate Single Tet

Generate Single Tet

- `Generate Regular Tet`: Generates a regular tetrahedron for inspection.
- `Generate Irregular Tet`: Generates an irregular tetrahedron for inspection.

Both actions follow [LDPM Generate Single Tet](#ldpm-generate-single-tet).

---

## <a id="3dcp-ldpm-generation"></a>3DCP-LDPM Generation

### <a id="3dcp-model-settings"></a>Model Settings

Model Settings

Contents: [Read Existing Model Setup File](#3dcp-read-setup) · [Cell Generation Parameters](#3dcp-cell-generation) · [MPI Parameters](#3dcp-mpi-parameters) · [Setup Sample Parameters](#3dcp-setup-samples)

#### <a id="3dcp-read-setup"></a>Read Existing Model Setup File

- `...`: Opens the model setup file browser and loads the selected parameter file.

#### <a id="3dcp-cell-generation"></a>Cell Generation Parameters

- `Number of CPUs`: Specifies the processor count used during cell and particle placement.
- `Number of Placement Increments`: Specifies the number of placement batches.
- `Maximum Placement Iterations`: Limits the placement attempts for each particle.
- `Placement Algorithm`: Selects `Trial-and-Error` placement.

#### <a id="3dcp-mpi-parameters"></a>MPI Parameters

- `MPI turn On/Off (External only)`: Selects `Off` or `On` for external MPI generation.
- `Rank`: Specifies the number of MPI processes used by the external workflow.
- `Number of Nodes`: Specifies the number of compute nodes.

#### <a id="3dcp-setup-samples"></a>Setup Sample Parameters

- `Number of Samples`: Specifies the number of model samples.

Cell generation, MPI, and sample settings follow [LDPM Model Settings](#ldpm-model-settings).

---

### <a id="3dcp-geometry"></a>Geometry

Geometry

#### Parameters

<ul style="list-style-type: disc;">
<li><code>Box</code>: Builds a rectangular solid.
<ul style="list-style-type: disc;">
<li><code>X:</code>: Sets the extent along X.</li>
<li><code>Y:</code>: Sets the extent along Y.</li>
<li><code>Z:</code>: Sets the extent along Z.</li>
</ul>
</li>
<li><code>Cylinder</code>: Builds a circular cylinder.
<ul style="list-style-type: disc;">
<li><code>Radius (along XY-Plane):</code>: Sets the radius in the horizontal plane.</li>
<li><code>Height (along Z-axis):</code>: Sets the height.</li>
</ul>
</li>
<li><code>L-shape</code>: Builds an L-shaped solid in the first octant (profile in the XY plane at Z = 0, extruded along +Z).
<ul style="list-style-type: disc;">
<li><code>Length in X:</code>: Outer extent of the horizontal leg along +X.</li>
<li><code>Length in Y:</code>: Outer extent of the vertical leg along +Y.</li>
<li><code>Width:</code>: Wall thickness of both legs in the XY plan (must be less than both lengths).</li>
<li><code>Thickness (along Z):</code>: Extrusion height of the profile along +Z (solid height).</li>
<li><code>Inside Fillet:</code>: Radius at the re-entrant (inside) corner where the legs meet; optional (0 = sharp corner).</li>
</ul>
Older parameter files with only four L-shape dimensions use the third value for both <code>Width</code> and <code>Thickness (along Z)</code>; the fourth value is the inside fillet.
</li>
<li><code>Custom</code>: Uses a solid already present in the document.
<ul style="list-style-type: disc;">
<li><code>Select Object</code>: Assigns the selected document solid as the specimen.</li>
<li><code>Deselect Object</code>: Clears that assignment.</li>
</ul>
</li>
<li><code>Import CAD or Mesh</code>: Uses an external geometry file. The solid must be closed.
<ul style="list-style-type: disc;">
<li><code>...</code>: Opens the geometry-file browser.</li>
</ul>
</li>
<li><code>Sweep-3DP</code>: Builds the printed solid by sweeping the selected cross-section along the selected path and stacking the layers.</li>
</ul>

<ul style="list-style-type: disc;">
<li><code>Profile Sketch (XZ or YZ plane)</code>: Selects the cross-section of one layer. This choice is independent of the path.
<ul style="list-style-type: disc;">
<li><code>Rectangular Profile (Layer Width/Height)</code>: Uses a rectangular cross-section.
<ul style="list-style-type: disc;">
<li><code>Layer Width:</code>: Sets the width of that rectangle.</li>
<li><code>Layer Height:</code>: Sets the height of that rectangle.</li>
<li><code>First Layer Properties</code>: Gives the first layer its own width and height. The next two fields are presented when this box is checked.</li>
<li><code>First Layer Width:</code>: Sets the width of the first layer.</li>
<li><code>First Layer Height:</code>: Sets the height of the first layer.</li>
</ul>
</li>
<li><code>Upload CAD File (Closed Loop)</code>: Uses a closed profile from a file.
<ul style="list-style-type: disc;">
<li><code>...</code>: Opens the profile-file browser.</li>
</ul>
</li>
</ul>
</li>
</ul>

<ul style="list-style-type: disc;">
<li><code>Path Sketch (XY plane)</code>: Selects the plan of the print. This choice is independent of the profile.
<ul style="list-style-type: disc;">
<li><code>Straight Line Path</code>: Prints along a straight line.
<ul style="list-style-type: disc;">
<li><code>Path Distance:</code>: Sets the length of the line.</li>
<li><code>Number of Layers:</code>: Sets how many layers are stacked on that line.</li>
</ul>
</li>
<li><code>Circular Path</code>: Prints along a circle.
<ul style="list-style-type: disc;">
<li><code>Path diameter:</code>: Sets the diameter of the circle.</li>
<li><code>Seam</code>: Marks a seam on the circular path. The next three fields are presented when this box is checked.</li>
<li><code>Seam position:</code>: Places the seam.
<ul style="list-style-type: disc;">
<li><code>Aligned</code>: Places the seam at the aligned position.</li>
<li><code>Random</code>: Places the seam at a random position.</li>
<li><code>Nearest</code>: Places the seam at the nearest position.</li>
</ul>
</li>
<li><code>Distance:</code>: Sets the seam offset.</li>
<li><code>Fillet at corner:</code>: Sets the corner radius at the seam.</li>
<li><code>Number of Layers:</code>: Sets how many layers are stacked on the circle.</li>
</ul>
</li>
<li><code>Square Path</code>: Prints along a square.
<ul style="list-style-type: disc;">
<li><code>Square Side:</code>: Sets the side length of the square.</li>
<li><code>Number of Layers:</code>: Sets how many layers are stacked on the square.</li>
</ul>
</li>
<li><code>FreeCAD Sketch</code>: Prints along a sketch in the document.
<ul style="list-style-type: disc;">
<li><code>Select Sketch:</code>: Names the sketch used as the path.</li>
<li><code>Create New Sketch</code>: Starts a new path sketch.</li>
<li><code>Select from Document</code>: Assigns an existing sketch.</li>
<li><code>Number of Layers:</code>: Sets how many layers are stacked on the sketch.</li>
</ul>
</li>
<li><code>Raster Slabs</code>: Prints parallel paths across a slab.
<ul style="list-style-type: disc;">
<li><code>Number of parallel paths:</code>: Sets how many parallel paths form the slab.</li>
<li><code>Spacing Between Paths:</code>: Sets the distance between those paths.</li>
<li><code>Corner Fillet:</code>: Sets the radius used where a path turns.</li>
<li><code>Raster Path Length:</code>: Sets the length of each parallel path.</li>
<li><code>Number of Slices:</code>: Sets how many direction changes divide the slab. A slice field is presented only while its number is within this count.</li>
<li><code>Slice 1 (in deg)</code>: Sets the direction of the first slice.</li>
<li><code>Slice 2 (in deg)</code>: Sets the direction of the second slice.</li>
<li><code>Slice 3 (in deg)</code>: Sets the direction of the third slice.</li>
<li><code>Slice 4 (in deg)</code>: Sets the direction of the fourth slice.</li>
<li><code>Slice 5 (in deg)</code>: Sets the direction of the fifth slice.</li>
<li><code>Slice 6 (in deg)</code>: Sets the direction of the sixth slice.</li>
<li><code>Slice 7 (in deg)</code>: Sets the direction of the seventh slice.</li>
<li><code>Slice 8 (in deg)</code>: Sets the direction of the eighth slice.</li>
</ul>
</li>
</ul>
</li>
</ul>

---

### <a id="3dcp-particles"></a>Particles

Particles

- `Minimum Particle Size in Simulation [mm]`: Sets the lower particle-size bound.
- `Maximum Particle Size in Simulation [mm]`: Sets the upper particle-size bound.
- `Particle Offset Parameter`: Controls the particle-placement offset.

Particle diameters and placement follow the same procedure as [LDPM Particles](#ldpm-particles). The printed geometry is the volume in which the particles are accepted.

---

### <a id="3dcp-mix-design"></a>Mix Design

Mix Design

#### Concrete Mix Design

- `Minimum Aggregate Size [mm]`: Sets the lower aggregate size used with the Fuller distribution.
- `Maximum Aggregate Size [mm]`: Sets the upper aggregate size used with the Fuller distribution.
- `Fuller Coefficient`: Sets the Fuller gradation between those two sizes.
- `Sieve Curve Diameters "[ mm , ... ]"`: Lists the sieve diameters. When this list and the passing list are both entered, the sieve curve replaces the Fuller distribution.
- `Sieve Curve Passing "[  -  , ... ]"`: Lists the cumulative fraction passing each sieve diameter.
- `w/c Ratio`: Sets the water-to-cement ratio. The water volume is this ratio multiplied by the cement content and divided by `Density of Water [tonne/mm3]`.
- `Cement Content [tonne/mm3 Concrete]`: Sets the cement mass in a unit volume of concrete. Divided by `Density of Cement [tonne/mm3]`, it becomes the cement volume fraction.
- `Fly Ash Content [tonne/mm3 Concrete]`: Sets the fly-ash mass in a unit volume of concrete. Divided by `Density of Fly Ash [tonne/mm3]`, it becomes the fly-ash volume fraction.
- `Silica Fume Content [tonne/mm3 Concrete]`: Sets the silica-fume mass in a unit volume of concrete. Divided by `Density of Silica Fume [tonne/mm3]`, it becomes the silica-fume volume fraction.
- `Other SCMs Content [tonne/mm3 Concrete]`: Sets the mass of the remaining supplementary materials. Divided by `Density of Other SCMs [tonne/mm3]`, it becomes their volume fraction.
- `Air Volume Fraction`: Sets the volume fraction occupied by air.
- `Density of Water [tonne/mm3]`: Converts the water mass, obtained from `w/c Ratio` and the cement content, into a volume fraction.
- `Density of Cement [tonne/mm3]`: Converts `Cement Content [tonne/mm3 Concrete]` into a volume fraction.
- `Density of Fly Ash [tonne/mm3]`: Converts `Fly Ash Content [tonne/mm3 Concrete]` into a volume fraction.
- `Density of Silica Fume [tonne/mm3]`: Converts `Silica Fume Content [tonne/mm3 Concrete]` into a volume fraction.
- `Density of Other SCMs [tonne/mm3]`: Converts `Other SCMs Content [tonne/mm3 Concrete]` into a volume fraction.

The aggregate volume fraction is the remainder after the volume fractions of cement, supplementary materials, water, and air have been removed.

---

### <a id="3dcp-reinforcement-parameters"></a>Reinforcement Parameters

Reinforcement Parameters

The active reinforcement selector provides `Fiber-Reinforcement` and `Fiber-Reinforcement - Sweep 3DP`.

#### Fiber-Reinforcement Parameters

##### Fiber-Reinforcement

- `Turn On/Off fibers`: Selects `Off` or `On`. `On` generates fibers inside the specimen.
- `Turn On/Off fiber cutting`: Selects `Off` or `On`. `On` shortens a fiber that would leave the specimen.
- `Diameter of fibers`: Sets the fiber diameter used to count the fibers and to record each fiber.
- `Length of fibers`: Sets the fiber length used to count the fibers and to place their end points.
- `Fiber volume fraction`: Sets the fiber volume as a fraction of the specimen volume. Together with the diameter and length, it determines the number of fibers.
- `Orientation`: Sets the three direction components. A zero value gives each fiber a random direction. Any other value is the preferred direction.
- `Preferredness for orientation`: Sets how closely the fibers follow `Orientation`. `0` is the widest scatter and `1` follows that direction.
- `File for fibers from CT scan`: Selects a measured fiber file. When a file is selected, those end points, directions, and lengths replace the generated fibers.
- `Perform fiber-facet intersection checks`: Selects `Off` or `On`. `On` records where fibers cross cell facets.
- `Create Fiber Output Files`: Selects `Off` or `On`. `On` saves the fiber end points, directions, lengths, diameter, and count.
- `Fiber crossing at interface`: Selects `Off` or `On`. `On` allows a fiber to cross an interface.

##### Fiber-Reinforcement - Sweep 3DP

- `Turn On/Off fibers`: Selects `Off` or `On`. `On` generates fibers inside the printed solid.
- `Turn On/Off fiber cutting`: Selects `Off` or `On`. `On` shortens a fiber that would leave the solid.
- `Diameter of fibers`: Sets the fiber diameter used to count the fibers and to record each fiber.
- `Length of fibers`: Sets the fiber length used to count the fibers and to place their end points.
- `Fiber volume fraction`: Sets the fiber volume as a fraction of the printed volume. Together with the diameter and length, it determines the number of fibers.
- `Orientation path`: Selects the print path whose direction the fibers follow.
  - `Straight Line Path`: Aligns fibers with the straight print path.
  - `Circular Path`: Aligns fibers with the circular print path.
  - `Square Path`: Aligns fibers with the square print path.
  - `FreeCAD Sketch`: Aligns fibers with a sketch. `Select sketch:` and `Segment length (mm)` are used for this choice.
  - `Raster Slabs`: Aligns fibers with the raster slices. A fiber is kept inside its layer and follows the active slice direction.
- `Select sketch:`: Names the sketch used when `Orientation path` is `FreeCAD Sketch`.
- `Segment length (mm)`: Sets the segment length used to sample the direction of that sketch.
- `Preferredness for orientation`: Sets the scatter about the path direction. `0` is the widest scatter and `1` follows the path.
- `Perform fiber-facet intersection checks`: Selects `Off` or `On`. `On` records where fibers cross cell facets.
- `Create Fiber Output Files`: Selects `Off` or `On`. `On` saves the fiber end points, directions, lengths, diameter, and count.
- `Fiber crossing at interface`: Selects `Off` or `On`. `On` allows a fiber to cross an interface.

The selected path is divided into segments. A trial fiber is kept when its starting point lies on one of those segments, and its direction follows that segment.

---

### <a id="additional-reinforcement-parameters"></a>Additional Reinforcement Parameters

Additional Reinforcement Parameters

- `Turn On/Off Additional Reinforcement`: Selects `Off` or `On`.
- `Conventional Reinforcement` and `Cable Reinforcement`: Select the active reinforcement type.

#### Conventional Reinforcement Parameters

- `Import CAD layout`: Opens the drawing fields. The file must be an ASCII DXF. Supported entities are `LINE`, `LWPOLYLINE`, `POLYLINE`, `ARC`, `CIRCLE`, `SPLINE`, and `INSERT` blocks made from those entities. Bulged segments are read as curves. Paper-space entities are ignored. Binary DXF and DWG files are not read.
- `Upload CAD layout`: Selects the DXF file. `...` opens the file browser.
- `Start position X, Y`: Sets the plan position of the drawing origin.
- `First layer height`: Sets the height of the first copy of the drawing.
- `Rotation`: Rotates the drawing about the vertical axis.
- `Positions along height`: Lists the additional heights at which the drawing is repeated.
- `Lines/Grid`: Builds straight bars in two directions and does not require a drawing.
  - `Start position X, Y`: Sets the plan origin of the grid.
  - `Direction 1`: Sets the first family of parallel bars.
    - `Line distance`: Sets the offset of the first bar from the start position.
    - `Primary direction`: Selects `X` or `Y` as the bar direction.
    - `Number of lines`: Sets how many parallel bars are created.
    - `Spacing`: Sets the distance between those bars.
    - `Height from XY plane`: Lists the heights of the bars.
  - `Direction 2`: Sets the second family of parallel bars.
    - `Line distance`: Sets the offset of the first bar from the start position.
    - `Primary direction`: Selects `X` or `Y` as the bar direction.
    - `Number of lines`: Sets how many parallel bars are created.
    - `Spacing`: Sets the distance between those bars.
    - `Height from XY plane`: Lists the heights of the bars.

#### Element Properties

- `Element Length`: Divides each bar into elements of this length.
- `Diameter`: Sets the bar diameter stored with the elements.
- `Element type`: Selects `2DOF-T2D2/LINK1` as the element type stored with the input.

#### Cable Reinforcement Parameters

- `Upload CAD layout`: Selects the cable DXF. The accepted entities are the same as `Import CAD layout`.
- `Start position X, Y`: Sets the plan position of the cable drawing.
- `First layer height`: Sets the height of the first cable copy.
- `Rotation`: Rotates the cable drawing about the vertical axis.
- `Positions along height`: Lists the additional cable heights.

#### Element Properties

- `Element Length`: Divides each cable into elements of this length.
- `Diameter`: Sets the cable diameter stored with the elements.
- `Element type`: Selects `2DOF-T2D2/LINK1` as the element type stored with the input.

---

### <a id="interlayer-parameters"></a>Interlayer Parameters

Interlayer Parameters

#### Interlayer

- `Interlayer`: Selects `No` or `Yes`. `No` leaves the printed solid without interlayer material numbers on the facets. `Yes` enables `Interlayer Type` and the `Parameters` group for the chosen scheme.
- `Interlayer Type`: Selects `Unidirectional Interlayer`, `Multidirectional Interlayer`, `Custom`, or `Fused Surfaces`. The contents of the `Parameters` group follow this choice.

#### Parameters

Shown for `Unidirectional Interlayer`.

- `First Layer Thickness`: Sets the thickness of the first printed layer measured along `Interlayer Axis`. It defines where the first interior region ends and the first interface band begins.
- `Layer Thickness`: Sets the thickness of each later layer along the same axis. Together with `First Layer Thickness`, it spaces the repeating interior and interface regions.
- `Interface Thickness`: Sets the thickness of each interface band between layers. Facets inside that band receive `Interlayer mF` instead of `Bulk mF`.
- `Interlayer Axis`: Selects `X`, `Y`, or `Z` as the direction in which layers and interfaces are stacked.
- `Bulk mF`: Sets the material number written to facets in the interior of each layer.
- `Interlayer mF`: Sets the material number written to facets inside each interface band.

#### Parameters

Shown for `Multidirectional Interlayer`.

- `First Layer Thickness`: Sets the thickness of the first layer along the primary stacking direction. It anchors the start of the repeating layer stack.
- `Layer Thickness`: Sets the thickness of each following layer in that stack. It controls the spacing between successive interface bands in the primary direction.
- `Layer Width`: Sets the in-plane width of each layer strip used when additional interface families are active. It works with the lower selector rows to place interfaces across the print plane.
- `Interface Thickness`: Sets the thickness of each interface band. The same value is applied to interfaces tagged with `X mF`, `Y mF`, `Z mF`, or `Overlap mF`.
- `Bulk mF`: Sets the material number of the layer interior away from the marked interfaces.
- `X mF`: Sets the material number for interface bands whose normal lies along X.
- `Y mF`: Sets the material number for interface bands whose normal lies along Y.
- `Z mF`: Sets the material number for interface bands whose normal lies along Z.
- `Overlap mF`: Sets the material number where interface bands from more than one family meet.
- Below `Overlap mF`, two selector rows add optional interface families. Each row offers `No` or `Yes`, then `X`, `Y`, or `Z`, then `Width` or `Height`. `Yes` activates that row; `Width` repeats interfaces across `Layer Width`, and `Height` repeats them through the layer thickness.

#### Custom

<ul style="list-style-type: disc;">
<li><code>Interface Thickness</code>: Sets the thickness of each custom interface band. Facets inside that band receive the interface material number from the active definition.</li>
<li><code>Bulk mF</code>: Sets the material number for facets outside those interface bands.</li>
<li><code>Custom Type</code>: Selects <code>4-Point Planar Surface</code>, <code>Path Ribbon (Planar)</code>, or <code>DXF / CAD Import</code>. The definition group below follows this choice.
<ul style="list-style-type: disc;">
<li><code>4-Point Planar Surface</code>: Records the interface layout in the text area under this title.
<ul style="list-style-type: disc;">
<li>A layer block opens with <code>Layer(X)</code>, <code>Layer(Y)</code>, or <code>Layer(Z)</code>. That letter is the normal direction of the interface plane.</li>
<li>Four corner lines list the in-plane coordinates of the interface patch. Each line supplies the two coordinates in that plane.</li>
<li>A closing list line such as <code>Z_list = [0, 5], mf=2</code> gives the positions along the layer normal and the interface material number for that block. Additional blocks may be added in the same text area.</li>
</ul>
</li>
<li><code>Path Ribbon (Planar)</code>: Places interfaces along a planar path defined in the text area under this title.
<ul style="list-style-type: disc;">
<li><code>Reference plane</code>: Selects <code>XY</code>, <code>XZ</code>, or <code>YZ</code> for the path coordinates in that text area.</li>
<li><code>Path type</code>: Selects <code>Line</code>, <code>Polyline</code>, or <code>Spline</code> as the curve along which the ribbon is swept.</li>
<li><code>Plane height</code>: Sets the coordinate along the plane normal for the ribbon. Further heights may be listed in the text area.</li>
<li>A <code>Path [</code> block lists points in the selected <code>Reference plane</code>. <code>Path type</code> controls how those points are connected.</li>
<li>Optional <code>X_list</code>, <code>Y_list</code>, or <code>Z_list</code> lines give positions along the normal and <code>mf=</code> for each ribbon. Generation tags facets within <code>Interface Thickness</code>.</li>
</ul>
</li>
<li><code>DXF / CAD Import</code>: Places interfaces from a drawing on a reference plane.
<ul style="list-style-type: disc;">
<li><code>DXF / CAD file</code>: Shows the selected drawing path. <code>...</code> opens the file browser.</li>
<li><code>Reference plane</code>: Selects <code>XY</code>, <code>XZ</code>, or <code>YZ</code> as the placement plane for the layout.</li>
<li><code>Plane height</code>: Sets the coordinate along the plane normal where the layout is placed.</li>
<li><code>Start position (X, Y, Z)</code>: Sets the translation of the drawing origin on that plane.</li>
<li><code>Interface mF</code>: Sets the material number for facets inside <code>Interface Thickness</code> of the imported interface geometry.</li>
</ul>
</li>
</ul>
</li>
</ul>

#### Parameters

Shown for `Fused Surfaces`.

- `Sweep Geometry Types`: Selects which sweep path from [Geometry](#3dcp-geometry) defines the fused contact surfaces. The path choice must match the active `Sweep-3DP` geometry so layer spacing and path length are read from that panel.
  - `Straight Line Path`: Marks interfaces along the straight sweep path and its layer stack.
  - `Circular Path`: Marks interfaces along the circular sweep path, including seam handling when it is enabled on the geometry panel.
  - `Square Path`: Marks interfaces along the square sweep path.
  - `FreeCAD Sketch`: Marks interfaces along the sketch named on the geometry panel.
  - `Raster Slabs`: Marks interfaces along each raster slice and its in-plane direction.
- `Interface directions`: Selects which contact orientations are tagged on the fused surfaces.
  - `X/Y`: Marks interfaces in the print plane when this box is checked.
  - `Z`: Marks interfaces between vertically stacked fused regions when this box is checked.
- `Interface Thickness`: Sets the thickness of each fused interface band measured normal to the contact surface.
- `Bulk mF`: Sets the material number of the fused layer interior away from the marked contacts.
- `X mF`: Sets the material number for fused interfaces whose normal lies along X.
- `Y mF`: Sets the material number for fused interfaces whose normal lies along Y.
- `Z mF`: Sets the material number for fused interfaces whose normal lies along Z.
- `Overlap mF`: Sets the material number where fused interface bands from more than one direction overlap.

---

### <a id="3dcp-mesh-preview"></a>Mesh Preview

Mesh Preview

#### Preview

- `Generate Surface Mesh`: Generates the preview surface mesh.
- `Open in Viewer`: Opens the generated preview.

The preview follows [LDPM Mesh Preview](#ldpm-mesh-preview) and uses the printed geometry.

---

### <a id="3dcp-model-generation"></a>Model Generation

Model Generation

Contents: [Status](#3dcp-generation-status) · [Output Directory](#3dcp-output-directory) · [Generation](#3dcp-generation-controls)

#### <a id="3dcp-generation-status"></a>Status

- `Status`: Displays the current operation.
- `Completion %p%`: Displays generation progress.

#### <a id="3dcp-output-directory"></a>Output Directory

- `...`: Selects the output directory.

#### <a id="3dcp-generation-controls"></a>Generation

- `Model Type`: Selects `Lattice Discrete Particle Model (LDPM)` or `Confinement Shear Lattice (CSL) - LDPM Style` .
- `Generate Model Data Files`: Writes the model data files when checked.
- `Generate Model Visualization Files`: Writes the model visualization files when checked.
- `Generate Single Tetrahedron and Cell Visualization Files`: Writes the single-tetrahedron and cell visualization files when checked.
- `Model Generation`: Builds the printed geometry, mesh, particles, cells, fibers, and additional reinforcement inside the current session.
- `External Model Generation`: Writes the external package described in [LDPM Model Generation](#ldpm-model-generation). The package also stores the sweep-path segments and, when a drawing is used, `reinforcement.dxf`.
- `Debug Model Generation`: Saves the mesh and then places particles, sweep or ordinary fibers, and facets in placement increments.
- `Write Parameters`: Writes the current panel values to `ldpmWorkbench.cwPar` and does not generate a model.

---

### <a id="3dcp-generate-single-tet"></a>Generate Single Tet

Generate Single Tet

- `Generate Regular Tet`: Generates a regular tetrahedron for inspection.
- `Generate Irregular Tet`: Generates an irregular tetrahedron for inspection.

Both actions follow [LDPM Generate Single Tet](#ldpm-generate-single-tet).

---

## <a id="sphdem-generation"></a>SPH/DEM Generation

### <a id="sphdem-model-settings"></a>Model Settings

Model Settings

Contents: [Read Existing Model Setup File](#sphdem-read-setup) · [Cell Generation Parameters](#sphdem-cell-generation)

#### <a id="sphdem-read-setup"></a>Read Existing Model Setup File

- `...`: Opens the model setup file browser and loads the selected parameter file.

#### <a id="sphdem-cell-generation"></a>Cell Generation Parameters

- `Number of CPUs`: Specifies the processor count associated with particle placement.
- `Number of Placement Increments`: Specifies the number of placement batches.
- `Maximum Placement Iterations`: Limits the placement attempts for each particle.
- `Placement Algorithm`: Selects `Trial-and-Error`.

`Number of Placement Increments` divides placement into batches. `Maximum Placement Iterations` limits the attempts for one particle. `Number of CPUs` is fixed in the panel, and `Placement Algorithm` provides `Trial-and-Error` as the active method.

---

### <a id="sphdem-geometry"></a>Geometry

Geometry

#### Parameters

<ul style="list-style-type: disc;">
<li><code>Truncated Cone</code>: Builds a cone with different radii at the two ends.
<ul style="list-style-type: disc;">
<li><code>Radius Bottom (along XY-Plane):</code>: Sets the radius at the bottom.</li>
<li><code>Radius Top (along XY-Plane):</code>: Sets the radius at the top.</li>
<li><code>Height (along Z-axis):</code>: Sets the height.</li>
</ul>
</li>
<li><code>Box</code>: Builds a rectangular solid.
<ul style="list-style-type: disc;">
<li><code>X:</code>: Sets the extent along X.</li>
<li><code>Y:</code>: Sets the extent along Y.</li>
<li><code>Z:</code>: Sets the extent along Z.</li>
</ul>
</li>
<li><code>Cylinder</code>: Builds a circular cylinder.
<ul style="list-style-type: disc;">
<li><code>Radius (along XY-Plane):</code>: Sets the radius in the horizontal plane.</li>
<li><code>Height (along Z-axis):</code>: Sets the height.</li>
</ul>
</li>
<li><code>Cone</code>: Builds a circular cone.
<ul style="list-style-type: disc;">
<li><code>Radius (along XY-Plane):</code>: Sets the base radius in the horizontal plane.</li>
<li><code>Height (along Z-axis):</code>: Sets the height.</li>
</ul>
</li>
<li><code>Arbitrary Prism</code>: Builds a regular prism.
<ul style="list-style-type: disc;">
<li><code>Polygon:</code>: Sets the number of sides.</li>
<li><code>Circumradius (along XY-Plane):</code>: Sets the radius of the circle through the corners.</li>
<li><code>Height (along Z-axis):</code>: Sets the height.</li>
</ul>
</li>
<li><code>Dogbone</code>: Builds a dogbone specimen.
<ul style="list-style-type: disc;">
<li><code>Length (along Z-axis):</code>: Sets the overall length.</li>
<li><code>Width (along X-axis):</code>: Sets the overall width.</li>
<li><code>Thickness (along Y-axis):</code>: Sets the thickness.</li>
<li><code>Gauge Length (along Z-axis):</code>: Sets the length of the reduced section.</li>
<li><code>Gauge Width (along X-axis):</code>: Sets the width of the reduced section.</li>
<li><code>Dogbone Type:</code>: Selects the transition at the reduced section.
<ul style="list-style-type: disc;">
<li><code>Rounded</code>: Uses a rounded transition.</li>
<li><code>Straight</code>: Uses a straight transition.</li>
</ul>
</li>
</ul>
</li>
<li><code>Import CAD</code>: Uses an external geometry file.
<ul style="list-style-type: disc;">
<li><code>...</code>: Opens the geometry-file browser.</li>
</ul>
</li>
</ul>

---

### <a id="sphdem-particles"></a>Particles

Particles

- `Minimum Particle Size [mm]`: Sets the lower particle-size bound.
- `Maximum Particle Size [mm]`: Sets the upper particle-size bound.
- `Fuller Coefficient`: Defines the particle gradation coefficient.
- `Sieve Curve Diameters "[ mm , ... ]"` and `Sieve Curve Passing "[  -  , ... ]"`: Define the sieve curve.
- `Particle Offset Coefficient`: Controls the particle-placement offset.

The diameter list is built on this panel. `Fuller Coefficient` or the sieve lists set the size distribution between `Minimum Particle Size [mm]` and `Maximum Particle Size [mm]`. Particles are ordered from largest to smallest and placed by trial and error inside the mesh. `Particle Offset Coefficient` keeps a clearance around each particle. The paste composition is taken from [Mix Design](#sphdem-mix-design).

---

### <a id="sphdem-mix-design"></a>Mix Design

Mix Design

#### Concrete Mix Design

- `w/c Ratio`: Defines the water-to-cement ratio.
- `Cement Content [tonne/mm3 Concrete]`, `Fly Ash Content [tonne/mm3 Concrete]`, `Silica Fume Content [tonne/mm3 Concrete]`, and `Other SCMs Content [tonne/mm3 Concrete]`: Define binder contents.
- `Air Volume Fraction`: Defines the air fraction.
- `Density of Water [tonne/mm3]`, `Density of Cement [tonne/mm3]`, `Density of Fly Ash [tonne/mm3]`, `Density of Silica Fume [tonne/mm3]`, and `Density of Other SCMs [tonne/mm3]`: Define constituent densities.

These fields describe the paste. Water volume follows `w/c Ratio` and the cement content. The remaining volume, after air and supplementary materials, is available for the particles defined on the Particles panel.

---

### <a id="sphdem-model-generation"></a>Model Generation

Model Generation

Contents: [Status](#sphdem-generation-status) · [Output Directory](#sphdem-output-directory) · [Generation](#sphdem-generation-controls)

#### <a id="sphdem-generation-status"></a>Status

- `Status`: Displays the current operation.
- `Completion %p%`: Displays generation progress.

#### <a id="sphdem-output-directory"></a>Output Directory

- `...`: Selects the output directory.

#### <a id="sphdem-generation-controls"></a>Generation

- `Model Type`: Selects `Discrete Fresh Concrete (DFC)`.
- `Generate Model`: Builds the geometry and mesh, then places the particles in batches and writes the fresh-concrete model.
- `External Model Generation`: Writes the mesh arrays, the parameter file, and `run_external_sphdem.py` to the output directory. Particle placement is run from that folder and is not finished in the current session.
- `Write Parameters`: Writes the current panel values and does not generate a model.

---

## <a id="rf-generation"></a>RF-Generation

### <a id="rf-model-settings"></a>Model Settings

Model Settings

Contents: [Read Existing Model Setup File](#rf-read-setup) · [Setup Sample Parameters](#rf-setup-samples)

#### <a id="rf-read-setup"></a>Read Existing Model Setup File

- `...`: Opens the random-field parameter file browser.
- `RF parameter file (.cwPar)`: Identifies the parameter-file format.

#### <a id="rf-setup-samples"></a>Setup Sample Parameters

- `Num of Realizations`: Specifies the number of realizations.
- `Seeds`: Specifies comma-separated random seeds.

`Num of Realizations` is the number of fields written in one run. `Seeds` starts the sampling. The first seed is used when several values are entered. `...` reloads a saved `.cwPar` parameter file into these panels.

---

### <a id="random-field-parameters"></a>Random Field Parameters

Random Field Parameters

#### Add Domain Field

- `X:`, `Y:`, and `Z:` define the domain dimensions.
- `Periodic:` provides `X`, `Y`, and `Z` periodicity selections.
- `Autocorrelation length`: Defines the correlation length.
- `Use different autocorrelation lengths for X, Y, and Z`: Enables separate directional correlation lengths.
- `Correlation Function`: Selects `square_exponential` or `exponential`.
- `Distribution Type`: Selects `Gaussian-truncated`.
- `Grid Spacing`: Defines the random-field grid spacing.
- `Upload Custom Grid Files`: Selects custom `.npy`, `.csv`, or `.txt` grid coordinates.

`X:`, `Y:`, and `Z:` bound the domain that a specimen must fit inside. A checked `Periodic:` direction repeats the correlation across that boundary. `Grid Spacing` sets the regular evaluation grid. `Upload Custom Grid Files` replaces that grid with the coordinates in the selected file.

#### Material properties

- `Elasticity:`, `Strength:`, and `Fracture (Energy):` identify the material-property rows.
- `COV(%)`: Specifies the coefficient of variation for each property.

#### Cross-correlation Matrix CC

- `Elasticity`, `Strength`, and `Fracture` identify the matrix axes.

---

### <a id="random-field-generation"></a>Random Field Generation

Random Field Generation

Contents: [Status](#rf-generation-status) · [Output Directory](#rf-output-directory) · [Generation](#rf-generation-controls) · [Advanced](#rf-advanced)

#### <a id="rf-generation-status"></a>Status

- `Status`: Displays the current operation.
- `Completion %p%`: Displays generation progress.

#### <a id="rf-output-directory"></a>Output Directory

- `Dir`: Specifies the optional output-directory name.
- `...`: Selects the output directory.

#### <a id="rf-generation-controls"></a>Generation

- `Model Type`: Selects `Random Field (RF)`.
- `Generate Model data and node files`: Enables model-data and node-file output.
- `Generate Model visualization VTK files`: Enables visualization-file output.
- `Generate Random Field`: Builds the correlated field and writes its folder.

`Generate Random Field` samples the three material properties on the domain grid. `Autocorrelation length` and `Correlation Function` set how quickly neighboring values change. `COV(%)` sets the scatter of each property, and `Cross-correlation Matrix CC` sets the dependence among elasticity, strength, and fracture energy. `Num of Realizations` and `Seeds` set how many fields are drawn and where the sampling starts. The folder stores the realizations, the grid, the correlation data needed to evaluate the field at new points, a generation report, and a copy of the parameter file. `Generate Model visualization VTK files` adds a visualization of the grid or of the evaluated points. [LDPM Random Field](#ldpm-random-field) imports this folder and assigns one realization to each sample.

#### <a id="rf-advanced"></a>Advanced

- `Sparse matrix representation`: Enables sparse representation.
- `Rank correlation (Spearman)`: Enables rank correlation.
- `Error Evaluation (QA plots)`: Starts error evaluation.

`Sparse matrix representation` stores the correlation data in sparse form. `Rank correlation (Spearman)` uses rank correlation among the three properties. `Error Evaluation (QA plots)` compares the generated distribution and correlation with the requested values and writes the check plots. It does not create a new field.

---

## <a id="ldpm-simulation-inputs"></a>LDPM Simulation Inputs

### <a id="import-mesh-bundle"></a>Import Mesh Bundle

Import Mesh Bundle

#### Import Bundle

- `No mesh bundle selected`: Indicates that no bundle has been selected.
- `Import mesh bundle folder`: Opens the folder browser.
- `...`: Selects the mesh-bundle folder.

The folder must contain the node file and the tetrahedron file produced by model generation. Facet, fiber, and reinforcement files are read when they are present. `No mesh bundle selected` remains until a valid folder is chosen.

---

### <a id="confirm-output"></a>Confirm Output

Confirm Output

#### Confirm Input Data

- Displays the imported model, component, mesh, and material information before file generation.

The display lists the element type, the parts, the node and tetrahedron counts, and the material properties found in the bundle. Facet, fiber, and reinforcement notes appear when those files were imported. File writing uses this confirmed bundle.

---

### <a id="write-file"></a>Write File

Write File

Contents: [Status](#simulation-status) · [Output Directory](#simulation-output-directory) · [Generation](#simulation-generation)

#### <a id="simulation-status"></a>Status

- `Status`: Displays the current operation.
- `Completion %p%`: Displays progress.

#### <a id="simulation-output-directory"></a>Output Directory

- `...`: Selects the output directory.

#### <a id="simulation-generation"></a>Generation

- `Generate Chrono Input File`: Writes the Project Chrono input package from the confirmed bundle.
- `Generate Abaqus Input File`: Writes the Abaqus input file from the same bundle.

Both actions use the imported nodes, tetrahedra, and any facet, fiber, or reinforcement data already confirmed. They do not rebuild the particle model.

---

## <a id="standalone-mesh-preview"></a>Standalone Mesh Preview

### <a id="standalone-mesh-preview-panel"></a>Mesh Preview

Mesh Preview

#### Mesh Preview

- `Mesh Type`: Selects `Surface mesh` or `VTK`.
- `Upload mesh file`: Uses `...` to browse mesh files.
- `Display mode`: Selects `Surface with edges`, `Surface`, or `Wireframe`.
- `Transparency`: Sets the transparency percentage.
- `Surface`: Selects the surface color.
- `Interface`: Selects the interface color.
- `Preview`: Displays the selected mesh files.

`Surface mesh` reads the surface written by a generation preview. `VTK` reads a visualization file. `Display mode` and `Transparency` change only the view. `Surface` and `Interface` set the colors of the solid surface and the interface surface. `Preview` draws the files selected with `Upload mesh file`.
