# Raw acoustic data

This folder contains the raw acoustic products used by the processing notebooks.

- `crater_floor_wav/` contains the retained Crater Floor WAV files and associated FITS products.
- `delta_upper_fan_zips/` contains the original downloaded Delta Front and Upper Fan ZIP archives.

The Crater Floor WAV files were originally converted from SuperCam FITS products. The original FITS-to-WAV conversion code for this early stage was not retained.

For the Delta Front and Upper Fan workflow, notebook 03 reads the relevant FITS products directly from the ZIP archives, extracts the SOUND shot arrays, and creates concatenated multishot WAV intermediates before calculating acoustic metrics.