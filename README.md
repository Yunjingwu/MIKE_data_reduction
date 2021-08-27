# MIKE_data_reduction

## Here's the notes of MIKE reduciton

### 0. Install related code:
Here're the installation steps of some related code. 

### 1. Initial Setup
#### 1.1 `IDL> mike_strct, mike` is used to make the mike structure. 
It is needed to change the `IFLT` to `TFLT`. 

#### 1.2 `IDL> mike_editstrct, mike` is used to modify the MIKE structure.

This step can help to mask bad images and further to check labels for each images. 
Labels are need in few of next steps. (Important!)

We only need the red-channel observations. Thus, `IDL> mike.setup = 1` helps to set all of the images as label **1**. 

Further, it is need to add tags for sources. The source j0757m0002 is labelled as **1** and standard star LTT_3218 labelled as **2**.
`IDL> mike[28:29].obj_id = 1`, `IDL> mike[32:35].obj_id = 1`, and `IDL> mike[38:39].obj_id = 2`, 

#### 1.3 `IDL> mike_wrstrct, mike, FITS='Mike_test.fits'` is used to write the MIKE structure to disk.

We can save all of the modifications to the fits file. `IDL> mike = mike_ar('Mike_test.fits')` will read the MIKE structure.


### 1. Initial Setup


