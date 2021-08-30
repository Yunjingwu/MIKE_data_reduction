# MIKE_data_reduction

## Here's the notes of MIKE reduciton

### 0. Install related code:
Here're the installation steps of some related code. 

### 1. Initial Setup
#### 1.1 `IDL> mike_strct, mike` is used to make the mike structure. 
It is needed to change the `IFLT` to `TFLT`. 

#### 1.2 `IDL> mike_editstrct, mike` is used to modify the MIKE structure.

This step can help to mask bad images and further to check labels for each images. 
Labels are need in few of next steps. **(Important!)**

We only need the red-channel observations. Thus, `IDL> mike.setup = 1` helps to set all of the images as label **1**. 

Further, it is need to add tags for sources. The source j0757m0002 is labelled as **1** and standard star LTT_3218 labelled as **2**.
`IDL> mike[28:29].obj_id = 1`, `IDL> mike[32:35].obj_id = 1`, and `IDL> mike[38:39].obj_id = 2`, 

#### 1.3 `IDL> mike_wrstrct, mike, FITS='Mike_test.fits'` is used to write the MIKE structure to disk.

We can save all of the modifications to the fits file. `IDL> mike = mike_ar('Mike_test.fits')` will re-load the MIKE structure.
`IDL> help, mike, /str` will help to confirm whether this code works.


### 2. Calibrations

#### 2.1 Bias subtraction (Useless)

In the MIKE reduction pipeline, bias subtraction is abandoned. Thus, we do not generate a bias frame.

#### 2.2 Gain Calibration

Some published gain values may not be appropriate for a given set of observations. 
`IDL> mike_setgain, mike, setup, [side]` setup is setted in ***step 1.2***. [side] == 2 represents the red channel.
`IDL> mike_setgain, mike, 1, 2`


#### 2.3 Generate Flats `mike_allflat` or splitted scripts

***This step requires `PS_Open`.***  Open it before generating the flat field.

`IDL> mike_mkmflat, mike, setup, [side], [/SMOOTH]` creates a stacked, normalized milky flat to correct pixel response variations.
If one's milky flats have many absorption features in them (not recommended), then one should call this routine with the /SMOOTH keyword.
`IDL> mike_mkmflat, mike, 1, 2`, `xatv, 'Flats/Flat_R_01_M.fits.gz'` helps to check the output flat field.

`IDL> mike_mktflat, mike, setup, [side]` combines the series of trace flats to create one high S/N image for order and slit tracing. 
`IDL> mike_mktflat, mike, 1, 2`. Then check the Trace Flat, `IDL> xatv, 'Flats/Flat_R_01_T.fits'`.

`IDL> mike_edgeflat, mike, setup, [side], /INTER, /CHK` trace the trace flat created above to determine the order curvature, and return a smooth fit.
`IDL> mike_edgeflat, mike, 1, 2, /INTER, /CHK`. Then check the edge `IDL> mike_chktrcflat, mike, 1, 2, /NOSTOP, /FIT`.

### 3. Arc Images 

`IDL> mike_allarc, mike, setup, [side]` Process all together. This is recommended If you are reducing a full night of data.
`IDL> mike_allarc, mike, 1, 2, /CLOBBER` ***NO mike.arc***

#### 3.1  measure the curvature of the arc lines
`IDL> mike_setarcm, raw_fil, setup, side`
`IDL> mike_setarcm, 'Raw/r0002.fits', 1, 2`, `mike_setarcm, 'Raw/r0003.fits', 1, 2`, `mike_setarcm, 'Raw/r0004.fits', 1, 2`, `mike_setarcm, 'Raw/r0029.fits', 1, 2`, `mike_setarcm, 'Raw/r0030.fits', 1, 2`, `mike_setarcm, 'Raw/r0033.fits', 1, 2`, `mike_setarcm, 'Raw/r0034.fits', 1, 2`, `mike_setarcm, 'Raw/r0039.fits', 1, 2`, `mike_setarcm, 'Raw/r0041.fits', 1, 2`, `mike_setarcm, 'Raw/r0044.fits', 1, 2`

#### 3.2  Process the Arcs. This step contains bias subtractions and flat fields the arc images (red side no bias subtraction?).
`IDL> mike_procarc, name, setup, obj_id, side`



### 4. Slit Profile
`IDL> mike_slitflat, mike, setup, [side], [/chk]` determines the slit profile for each order. 
`IDL> mike_slitflat, mike, 1, 2, /chk, /clobber`

### 5. Extract spectra 
`IDL> mike_allobj, mike, setup, obj_id, side, [exp], /PROCALL` includes processing, CR rejection, tracing, sky subtraction and extraction. 
(multiple exposures of single object will be reduced together)

`IDL> mike_allobj, mike, 1, 1, 2, /procall`, `mike_allobj, mike, 1, 2, 2, /procall, /nocr` ***error***

`IDL> mike_proc, mike, SETUP=1, OBJ=1, /CLOBBER`-> `IDL> mike_objcr, mike, 1, 1, 2, /NOCHK`-> `mike_fntobj, mike, 1, 1, 2, /CHK`



