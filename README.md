# Visualization and Analysis of FITS
## Visualization and analysis of astronomical images
This repository contains scripts for visualizing astronomical data from FITS files. It includes the fits_data_visualization.py script for detailed slice-by-slice visualization, the fits_volume.py script for 3D volume visualization, and test scripts to validate their functionalities.
## Prerequisites
- Python 3
- Plotly
- Astropy
- Numpy
- Matplotlib
- OpenCV (for testing_visualization.py)
- Google Colab (for fits_drive_upload.py)
## Installation
Ensure you have the required libraries installed. You can install them using pip:
%pip install plotly astropy numpy matplotlib opencv-python
## Main Script: fits_data_visualization.py
### Detailed Description
The fits_data_visualization.py script is designed to visualize astronomical data contained in FITS files in a detailed and interactive manner. This script is particularly focused on providing a clear view of each individual slice within the FITS file, alongside computed astronomical parameters such as brightness and distance.
### Usage
1- Download FITS File: Obtain a FITS file containing astronomical data. These files are typically used in astrophysics and astronomy for storing image data, spectral data, or other multi-dimensional datasets.
2- Set File Path: In the script, locate the fits_file_path variable and set it to the path of your downloaded FITS file.
3- Run the Script: Execute the script in your Python environment. You can run it from a terminal or an IDE that supports Python:

python fits_data_visualization.py
### How it works?
- Open FITS File: The script uses Astropy's fits module to open and read data from the specified FITS file. Astropy handles the FITS standard, allowing for efficient and accurate data manipulation.
- Preprocess Data: The data is preprocessed to handle NaN and infinite values, ensuring that the dataset is clean and ready for visualization.
- World Coordinate System (WCS) Conversion: The World Coordinate System (WCS) is an essential component in the field of astronomy, which allows for the mapping of pixel coordinates in two-dimensional image data to multi-dimensional celestial coordinates such as Right Ascension (RA) and Declination (Dec).
  * How WCS Conversion Works?
  When a FITS file is read, the WCS information embedded in the file's header is extracted. This metadata contains the necessary parameters to transform pixel coordinates (x, y) to their corresponding celestial coordinates (RA, Dec). The conversion uses the following formula:

RA = WCS_RA_REF + (x - WCS_PIX_REF_X) * WCS_DELTA_RA
Dec = WCS_DEC_REF + (y - WCS_PIX_REF_Y) * WCS_DELTA_DEC

Here, WCS_RA_REF and WCS_DEC_REF are the reference coordinates for RA and Dec, while WCS_DELTA_RA and WCS_DELTA_DEC are the increments per pixel. WCS_PIX_REF_X and WCS_PIX_REF_Y are the reference pixel coordinates.

- User Input for Distance Calculation: The script prompts the user to choose between 'near' or 'far' for the distance calculation method. This choice affects how the script computes distances for each slice of the FITS file, which is essential for accurate visualization.
  * Near and Far Distance Calculations
  The calculation of distance is based on the mean velocity observed in each slice, as well as the user's input. The script utilizes the following formulas to compute the 'near' and 'far' distances:

R = Vc / (|vel| / (Ro * sin(lu)) + (Vo / Ro))
dR = sqrt(R^2 - (Ro * sin(lu))^2)
Near Distance (d_near) = tp - dR
Far Distance (d_far) = tp + dR

R is the galactocentric distance.
vel is the mean velocity in each slice.
Vc is the velocity of the local standard of rest (LSR) in circular orbit around the Galactic center.
Ro is the distance from the Sun to the Galactic center.
Vo is the velocity of the Sun with respect to the local standard of rest.
dR is the distance along the line of sight from the tangent point to the slice.
tp is the tangent point distance, which is Ro * cos(lu).
lu is the galactic longitude converted from degrees to radians, lu = (360 - l) * pi / 180.

These formulas take into account the observed velocities and the geometry of the Milky Way to calculate the distances. The 'near' distance assumes the object is closer to us than the tangent point, whereas the 'far' distance assumes it is further away.

