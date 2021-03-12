## Region alignment between IF images and .mcds

This folder contains documentation and resources to convert ROI coordinates defined on whole slide immunoflourescence (IF) images into coordinates on raw .mcd images.
The purpose of doing so is to define ROIs based in manually gated cell proportions and automatically find these regions on the slides for IMC acquisition.

## Software

The alignment builds on the open-source software [napari](https://napari.org/).
[Jonas Windhager](https://github.com/jwindhager) has build the [napari-imc](https://github.com/BodenmillerGroup/napari-imc), [napari-czifile2](https://github.com/BodenmillerGroup/napari-czifile2) and [napping](https://github.com/BodenmillerGroup/napping) tools to help reading in .czi and .mcd files and align them.

## Issues

If you have problems with `napping`, please raise an issue [here](https://github.com/BodenmillerGroup/napping/issues).

## Usage

For each batch of slides, the following steps need to be performed.

## Set up folder structure

1. Create the folder `immucan_volume/raw_data/Panel_1/BatchXYZ/IF_regions`
2. Create the folder `zip_files` in the previously created folder
3. Download the `.zip` files as obtained by the regions selection tool [LINK]() to the `zip_files` folder
4. Run the script `bash scripts/create_folder.sh immucan_volume/raw_data/Panel_1/BatchXYZ/IF_regions`

## Set up `napping` environment

In the next step, you will need to install [napping](https://github.com/BodenmillerGroup/napping) and its dependencies:

```
conda create -n napping python
conda activate napping
pip install napping napari-imc napari-czifile2
```

## Align the images

From within the `conda` environment, `napping` can be started from the command line:

```
napping
```

This call will open a GUI to select files for mapping.

**Note for Mac users:** The mounted volume is not visible by default. 
You will need to type `/Volumes/immucan_volume` into the fields and click on the folder icon.
From there you can select the appropriate folders.

### 1. Generation of the slidescanner to mcd transformation

In the first step, you will need to calculate the "post-transformation". 
This is defined as the transformation from coordinates on the slidescanner image to the mcd panoramas.
In the `napping` GUI, please select the `Slidescanner` folder as `Source Images` and the `mcd_files` folder as `Target images`.

When selecting the "Directories (multiple file pairs)" option, you will need to perform file matching via regular expressions.
For the IMMUcan slides, please insert `[0-9]+-[A-Z]+-` in all `RegEx` fields.

Please select `control_points_posttransformation` as `Control point dest.` for the slidescanner to mcd transformation. The transformation in `.pickle` format (`Transform dest`) will be stored in the `czi_to_mcd_transformation` folder. 

Please select `Affine` as `Transform type`. All other fields remain empty.

For each image pair, at least 4 control point pairs need to be set. Please observe the number of matched points and the residual mean error.
All control points and the transformation matrices are written out automatically in the background.
You can close `napping` once you are done.

### 2. Mapping IF images to mcds

In the second step, we will use one of the previously generated "post-transformation" matrices to help the transformation from IF images to mcd.
The selection of the "best" post-transformation matrix is not trivial and requires further investigation.

Please open `napping` a second time now. You should select the `IF_images` folder as `Source Images` and the `Slidescanner` folder as `Target images`.
Please select the "Directories (multiple file pairs)" option and enter `[0-9]+-[A-Z]+-` in all `RegEx` fields.
Please select `control_points` folder as `Control point dest.` for the IF to slidescanner transformation. The transformation in `.pickle` format (`Transform dest`) will be stored in the `jpg_to_czi_transformation` folder. 

Please select `Affine` as `Transform type`.

Here, we will also need to specify the `Source coordinates` which are located in the `csv_coordinates` folder. The `Transformed coord. dest` location is the `csv_coordinates_transformed` folder.
The `Pre-transform` field will remain empty and for the `Post-transform` you will need to specify one of the "post-transformation" marices.

The transformed coordinates are now available and can be further processed to be used for IMC acquisition.

### Post-process the transformed coordinates

In the final step, please run:

```
Rscript scripts/post_process.R immucan_volume/raw_data/Panel_1/BatchXYZ/csv_coordinates_transformed
```

After this, the ROIs can be loaded into the Fluidigm software.