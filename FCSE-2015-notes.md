# Meeting Notes from Future Challenges in Shear Estimation

## Tuesday, November 10

Discussion started with a brainstorming exercise on simulation features
and systematic effects that had not been included in previous STEP/GREAT
challenges:

- Blending
- Detection/selection
- Cosmic rays
- Chromatic effects (both PSFs and galaxies)
- Nonlinear shear
- Flexion
- Photometric redshifts
- Realistic atmospheric PSFs
- Multiple epochs with realistic dithers and distortions
- Combining data from multiple instruments
- Dealiasing
- Realistic training samples
- Morphology evolution
- PSF star selection
- Magnification
- Tomography
- Sufficient precision for Stage IV
- Intrinsic alignments

This brought up a number of sensor effects:

- Saturation and bleed trails
- Brighter-fatter
- Stray and scattered light
- Nonlinear pixel response
- Pixel noise covariance (e.g. interpixel capacitance)
- Pixel size and shape variations
- Charge transfer efficiency
- Persistence
- Crosstalk


We also put together a list of constraints on what we could accomplish in GREAT4, considering the state of various surveys and available effort:

- Data volume / CPU time: ring tests confuse several effects we'd like to explore, but without them we need many more galaxies.  We'd need more anyway to approach Stage IV precision.
- Accessibility: if a simulation is as difficult to process as a real survey, no one will invest the time to do it.
- Human effort (both organizers and participants): everyone is more busy with surveys than they were for GREAT3.
- Cross-Survey Usefulnes: no need to do work under the GREAT umbrella if it's only useful for one survey, or if it's already being done separately by all the surveys.
- Politics: big collaborations have collective opinions that have to be considered, because they probably can't be changed.
- Conclusiveness: too much complexity and/or ill-defined tests can make it hard to draw any conclusions if nothing works perfectly.

We then approached the problem from two angles:

- First, we considered developing a software suite that would enable large simulations to be run directly by users, with a small number of predefined configurations to improve reproduceability.  This may preclude truly blind challenges, but it seems to be the only practical way to deal with the large data volume required by any of the things we're most interested in testing.  In particular, this suite would extend GalSim to include generation of input galaxy catalogs and large-scale simulation of full focal-plane images that approximate those of real cameras.  There was good consensus that this work would be very broadly useful, but we did not go into details on features or on planning the work.

- We then considered the easiest possible challenge we could build that would still be considered useful.  Selection and blending effects were quickly highlighted as both a high priority and a relatively easy project; chromatic effects and multi-epoch imaging with realistic atmospheric PSFs were raised as similarly high-priority issues that would be considerably more difficult to simulate to sufficient accuracy.  Even for selection and blending, however, data volumes would probably require an approach in which participants run the simulations themselves (which requires at least some progress on the simulation suite mentioned above).

## Wednesday, November 11

In our second discussion session, we discussed the steps necessary to build a simulator capable of testing selection/blending, chromaticity, and multi-epoch imaging.

While GalSim is already capable of producing images that contain all the necessary features to test selection and blending, it requires an external catalog and a fair amount of custom wrapper code to do this at the necessary scale, which means there's still quite a bit of work to do before we could trivially run the same large-scale simulation at multiple locations.  This work includes:

- Build a "config file generator" that produces detailed GalSim config files given a target instrument and a much more limited configuration file indicating the observing conditions and the sky catalog.

- Build a "sky catalog generator" that combines real galaxy images (possibly synthesized images, but not simple analytic models) with some prescription for clustering and density-dependent morphology.

- Build instrument-dependent modules to support the config file generator, for both real (existing and future) instruments and possibly some imaginary generic instruments.  It is up to each collaboration to provide its own instrument module.  Some instrument modules already exist, but probably aren't sufficient to do everything the config-file generator will need, and we may need to standardize the interface more than has been done so far.

- Provide scripts for large-scale batch processing using the GalSim config interface, to allow easy parallelization over many nodes using common batch systems.

- Decide how to represent intermediate truth images and catalogs (e.g. PSF models at galaxy positions, unblended galaxy postage stamps), and develop a pipeline framework that allows users to test only small pieces of the pipeline while relying on truth information or fiducial algorithms for the rest.

To support investigation of chromatic effects, we'd need to do a lot more work:

- The config-file generator would need to extended to support multi-band observations.

- There is experimental functionality in GalSim that assumes that galaxies can be written as a sum of components with wavelength-independent morphologies and morphology-independent SEDs, but we still need to create the basis set from real data and find a way to validate the approach.

- Instrument-specific modules would need to be enhanced to produce transmission curves and wavelength-dependent PSFs.

To support a useful investigation of multi-epoch imaging from the ground, we'd need a better way of simulating atmospheric PSFs in GalSim.  This need not be fully realistic if we just want to investigate how varying seeing affects algorithms for blended shear estimation and/or correction for chromatic effects without fully testing PSF estimation.

Given that different contributors have different priorities, we'll work on as many of these as have volunteers in parallel, and reconvene in mid-2016 to evaluate how far we've gotten and decide on the next steps.
