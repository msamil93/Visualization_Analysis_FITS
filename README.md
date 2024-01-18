# Visualization and Analysis of FITS

## Visualization and Analysis of Astronomical Images
This repository contains scripts for visualizing astronomical data from FITS files. It includes the `fits_data_visualization.ipynb` script for detailed slice-by-slice visualization, the `fits_volume.ipynb` script for 3D volume visualization, and test scripts to validate their functionalities.

## Prerequisites
- Python 3
- Plotly
- Astropy
- Numpy
- Matplotlib
- OpenCV (for `testing_visualization.ipynb`)
- Google Colab (for `fits_drive_upload.ipynb`)

## Installation
**Ensure you have the required libraries installed. You can install them using pip:** 

```pip install plotly astropy numpy matplotlib opencv-python```
<br><br>
## Main Script: fits_data_visualization.ipynb
### Detailed Description
The `fits_data_visualization.ipynb` script is designed to visualize astronomical data contained in FITS files in a detailed and interactive manner. This script is particularly focused on providing a clear view of each individual slice within the FITS file, alongside computed astronomical parameters such as brightness and distance.

### Usage
1. **Download FITS File**: Obtain a FITS file containing astronomical data. These files are typically used in astrophysics and astronomy for storing image data, spectral data, or other multi-dimensional datasets.

2. **Set File Path**: In the script, locate the `fits_file_path` variable and set it to the path of your downloaded FITS file.

3. **Run the Script**: Execute the script in your Python environment. You can run it from a terminal or an IDE that supports Python.

### How it works?
- **Open FITS File**: The script uses Astropy's fits module to open and read data from the specified FITS file. Astropy handles the FITS standard, allowing for efficient and accurate data manipulation.
  
- **Preprocess Data**: The data is preprocessed to handle NaN and infinite values, ensuring that the dataset is clean and ready for visualization.

- **World Coordinate System (WCS) Conversion**: The World Coordinate System (WCS) is an essential component in the field of astronomy, which allows for the mapping of pixel coordinates in two-dimensional image data to multi-dimensional celestial coordinates such as Right Ascension (RA) and Declination (Dec).

  #### How WCS Conversion Works?
  When a FITS file is read, the WCS information embedded in the file's header is extracted and utilized to transform pixel coordinates (x, y) to their corresponding celestial coordinates (RA, Dec). The `astropy` library's `WCS` module provides a comprehensive method for this conversion.

  1. **Pixel Corners Definition:**
     The corner pixel coordinates of the FITS image data are defined, representing the extremities of the image in pixel space.

     ```python
     pixel_corners = np.array([
         [0, 0, 0],  # Top-left corner
         [fits_data.shape[2] - 1, 0, 0],  # Top-right corner
         [0, fits_data.shape[1] - 1, 0],  # Bottom-left corner
         [fits_data.shape[2] - 1, fits_data.shape[1] - 1, 0]  # Bottom-right corner
     ])
     ```

  2. **Transformation with `all_pix2world`**:
  The `all_pix2world` method is applied to these pixel coordinates. This process involves several steps:

    - **Reference Point Adjustment:** The pixel coordinates are referenced to a specific point in the image, usually central or of scientific significance.

    - **Scale and Orientation Application:** Scaling factors and rotational parameters are applied to align the pixel grid with the celestial coordinate grid.

    - **Projection to Celestial Sphere:** The aligned coordinates are projected onto the celestial sphere, converting them into RA and Dec coordinates.

  This method accounts for potential distortions and non-linear mappings in the image data.

        ```python
        wcs_corners = wcs.all_pix2world(pixel_corners, 0)
        ra_corners = [wcs_corner[0] for wcs_corner in wcs_corners]
        dec_corners = [wcs_corner[1] for wcs_corner in wcs_corners] 

   3. **Output**:

  The result is an array of celestial coordinates (RA and Dec) corresponding to the input pixel coordinates. This accurate mapping is essential for astronomical analysis and research.
  
  The `wcs.all_pix2world` method ensures a precise and adaptable transformation of coordinates, suitable for the diverse WCS configurations in different FITS files.

  <br>

- **User Input for Distance Calculation**: The script prompts the user to choose between 'near' or 'far' for the distance calculation method. This choice affects how the script computes distances for each slice of the FITS file, which is essential for accurate visualization.

  #### Near and Far Distance Calculations
  The calculation of distance is based on the mean velocity observed in each slice, as well as the user's input. The script utilizes the following formulas to compute the 'near' and 'far' distances:

  ```python
  R = Vc / (abs(vel) / (Ro * sin(lu)) + (Vo / Ro))
  dR = sqrt(R**2 - (Ro * sin(lu))**2)
  Near Distance (d_near) = tp - dR
  Far Distance (d_far) = tp + dR

Where:
- `R` is the galactocentric distance.
- `vel` is the mean velocity in each slice.
- `Vc` is the velocity of the local standard of rest (LSR) in circular orbit around the Galactic center.
- `Ro` is the distance from the Sun to the Galactic center.
- `Vo` is the velocity of the Sun with respect to the local standard of rest.
- `dR` is the distance along the line of sight from the tangent point to the slice.
- `tp` is the tangent point distance, which is Ro * cos(lu).
- `lu` is the galactic longitude converted from degrees to radians, lu = (360 - l) * pi / 180.

These formulas take into account the observed velocities and the geometry of the Milky Way to calculate the distances. The 'near' distance assumes the object is closer to us than the tangent point, whereas the 'far' distance assumes it is further away.

### Brightness and Opacity Calculation
In the `fits_data_visualization.ipynb` script, brightness is a critical factor in visualizing each slice of the FITS data. Brightness is determined by analyzing the pixel values within each slice. Here's how the process works:

#### Pixel Value Analysis
Each pixel in a slice contains data that, when aggregated, represents the total light or energy captured from that particular region of space.

#### Average Brightness
The average brightness for each slice is calculated by taking the mean of all pixel values. This average gives us a single value that represents the overall brightness of the slice.

#### Normalized Brightness
To ensure that brightness values are comparable across all slices, they are normalized. This involves dividing the brightness of each slice by the maximum brightness found in the entire dataset, resulting in a range between 0 (least bright) and 1 (most bright).

### Opacity as a Visual Indicator
Opacity in the visualization serves as a visual indicator of brightness. By setting the opacity of each slice to its normalized brightness level, the following is achieved:

- **Visibility of Brightness Differences:** Slices with higher brightness will appear more opaque, making them stand out against slices with lower brightness, which will be more transparent.

- **Intuitive Interpretation:** This approach to setting opacity allows for an intuitive understanding of the data; areas with greater emissions or energy are naturally more visible.

- **Enhanced Depth Perception:** Using opacity in this way also helps to give a sense of depth to the visualization, as it simulates the way light interacts with materials in the real world.

![Plotly Toolbar](https://github.com/msamil93/Visualization_Analysis_FITS/blob/main/assets/visualisation_neardist.png?raw=true)
### Distance Calculation
Distance calculations in the script are based on the user's selection of the 'near' or 'far' method. These calculations are rooted in astronomical principles and involve the following steps:

#### Velocity Calculation
For each slice, the mean velocity depend on the pixel values. This velocity is related to the Doppler effect, where the motion of celestial objects causes a shift in the frequency of light.

#### Galactic Rotation Curve
Using the galactic rotation curve, which describes the velocity of objects within the galaxy as a function of their distance from the center, the radial distance of the object from the observer is calculated. 
#### Distance Determination
Depending on whether 'near' or 'far' is selected, the script uses above given mathematical model to compute the distance to each slice, considering the object's position within the Milky Way and its motion relative to us.

These calculations allow for the accurate representation of each slice's position in space, providing additional context to the visualization and enhancing the overall understanding of the data.


### 3D Visualization with Plotly:
The script employs Plotly's Mesh3d to create a 3D interactive visualization. Each slice is represented with its corresponding brightness and distance, allowing for a detailed exploration of the data. The visualization is interactive, enabling users to rotate, zoom, and pan through the data for thorough examination.

#### Interactive Exploration with Plotly Toolbar
The visualization created by `fits_data_visualization.ipynb` includes an interactive toolbar located at the top right corner of the visualization pane. This toolbar provides various tools that enhance the interactivity and usability of the visualization, allowing users to:

![Plotly Toolbar](https://github.com/msamil93/Visualization_Analysis_FITS/blob/main/assets/Plotly%20Toolbar.png?raw=true)


- **Zoom In/Out:** Magnify or shrink the view of the visualization.
- **Pan:** Click and drag to move the visualization within the pane.
- **Orbit Rotation:** Click and drag to rotate the 3D visualization around its center point.
- **Turntable Rotation:** Rotate the visualization as if it were on a turntable.
- **Reset Camera:** Return the view to the original state.
- **Reset Camera to last save:** Return the view to the last save.
- **Snapshot:** Take a snapshot of the current view of the visualization.

Each of these tools can be clicked to activate or deactivate, providing a flexible way to interact with the data. Additionally, users can use the camera icons to switch between different preset views of the visualization, such as top-down or side views.
<br><br>

## Additional Script: fits_volume.ipynb

### Usage
Similar to the main script, `fits_volume.ipynb` visualizes the data as a 3D volume. To run the script in a Python environment, use the following command:

```python fits_volume.ipynb```

![Plotly Toolbar](https://github.com/msamil93/Visualization_Analysis_FITS/blob/main/assets/volume2.png?raw=true)

### How It Works
- Reads data from a FITS file and visualizes it as a continuous 3D volume.
- Useful for visualizing the overall structure and distribution of the data in three dimensions.


![Plotly Toolbar](https://github.com/msamil93/Visualization_Analysis_FITS/blob/main/assets/volume_neardist_close.png?raw=true)
<br>

## Test Script: fits_drive_upload.ipynb

### Usage
This script is designed for Google Colab. It mounts Google Drive, reads a FITS file, and saves each slice as an image in a specified directory.
Can be used to prepare data for testing `testing_visualization.ipynb`.

### How It Works
- Mounts Google Drive and reads a FITS file.
- Saves each slice of the FITS file as an image in Google Drive.
- These images can later be downloaded and used for testing.
<br>

## Test Script: testing_visualization.ipynb

### Usage
- Download the images saved by `fits_drive_upload.ipynb`.
- Update the `folder_path` variable to point to the directory containing the images.
- Run the script in a Python environment using the following command:

```python testing_visualization.ipynb```

### How It Works
- Reads images from a specified directory.
- Visualizes them in 3D space using Plotly's Mesh3d.
- Tests the opacity feature by displaying the images with a fixed opacity.
<br>

## Test Script: testing_volume.ipynb

### Usage
Run the script in a Python environment using the following command:

```python testing_volume.ipynb```
### How It Works
- Reads data from a FITS file.
- Visualizes it as a 3D volume using Plotly.
- Validates the functionality of `fits_volume.ipynb`. <br><br>

## Customization
- Modify FITS file paths and other parameters to visualize different datasets.
- Explore different visualization styles and parameters in `fits_data_visualization.ipynb` and `fits_volume.ipynb`. <br>

## Contributions
Contributions to this project are welcome! If you have suggestions for improvements or new features, feel free to create an issue or submit a pull request. <br>

### How to Contribute
- Fork the repository.
- Create a new branch for your feature.
- Add your changes and commit them.
- Push to your fork and submit a pull request. <br>

### Contribution Guidelines
- Please provide a clear and detailed explanation of your changes or additions.
- Ensure your code adheres to the existing style of the project to maintain consistency.
- Update the documentation accordingly if your changes are significant. <br><br>

## Support and Documentation
For additional information, usage instructions, and support, please refer to the documentation provided in the repository. If you encounter any issues or have questions, open an issue in the repository, and we will do our best to help.

