# Raw acoustic data

Raw acoustic products are not included in this repository because of file size.

Expected local folder structure:

```text
data/raw/
├── crater_floor_wav/
└── delta_upper_fan_zips/
    ├── Delta/
    └── Upper Fan/
```

Crater Floor WAV files were originally converted from SuperCam FITS products. The original FITS-to-WAV conversion code for this early stage was not retained.

For the Delta Front and Upper Fan workflow, the processing notebook reads SuperCam FITS products from the downloaded ZIP archives, extracts SOUND shot arrays, and creates concatenated multishot WAV intermediates before calculating acoustic metrics.
