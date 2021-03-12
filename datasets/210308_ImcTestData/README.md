## IMC mock dataset

This dataset was generated on 05.03.2021 using CyTOF Software 7.0 for Imaging Mass Cytometry.

## Dataset

There are 5 different .mcd files (zipped and unzipped) each containing 3 ROIs and one panorama.
Each ROI is between 50x50 and 100x100 pixels and contains 5 channels.

## Panel

The panel for this dataset looks as follows:

| Tube Number | Metal Tag | Target
| :---	      | :---	  | :---
| 625	      | Sm147	  | Laminin
| 1378	      | Yb172	  | H3K27Ac
| 2266	      | Pr141	  | Cytokeratin 5
| 2359	      | Eu153	  | YBX1
|             | Ag107	  | Ag107

## Processing

The raw data was processed using CellProfiler v4.0.7 and Ilastik v1.3.3post3.
For this, `ImcSegmentationPipeline` v2.0 was used. 

## Known issues

Channel Pr141 is only detectable in `20210305_NE_mockData1` and `20210305_NE_mockData3`.
Channel Ag107 is only detectable in `20210305_NE_mockData1`.
`ROI_001` of `20210305_NE_mockData1` is empty (no signal).
