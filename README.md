# LBADataHandler Documentation

## LBA - Lipid Brain Atlas and MALDI-MSI Data
MALDI-MSI (Matrix-Assisted Laser Desorption/Ionization Mass Spectrometry Imaging) is a powerful technique at the intersection of mass spectrometry and spatial tissue analysis. By preserving the spatial information of molecules within biological samples, MALDI-MSI offers insights into the distribution of biomolecules across tissue sections (the brain in this case).

This acquisition technique works by applying a chemical matrix to a thin tissue section and using laser irradiation to ionize both matrix and sample molecules. As the mass spectrometer analyzes these ions, it maintains their original spatial coordinates, generating position-specific mass spectra. This allows researchers to create detailed molecular maps of tissue samples, revealing the distribution and concentration of various compounds.

A significant challenge in MALDI-MSI analysis has been noise measurement and consistent annotation across brain sections. To address this, the data processing pipeline employs [uMAIA](https://github.com/lamanno-epfl/uMAIA). The processed data is then registered to the Common Coordinate Framework (CCFv3) from the Allen Brain Atlas, enabling standardized spatial analysis and comparison. 

This comprehensive processing pipeline forms the foundation of the Adult Mouse Lipid Brain Atlas (LBA), providing a standardized framework for lipid analysis across brain sections. The LBA project, curated by the [Laboratory of Brain Development and Biological Data Science at EPFL](https://www.epfl.ch/labs/nsbl/), aims to provide a standardized reference system for lipidomic data. As an atlas, it emphasizes the precise localization of lipid expressions within the brain, which is crucial for understanding the organ’s functional architecture and the interactions between its components.

aggiungi immagine di MALDI e uMAIA della tesi

## Input Data Requirements

The class processes MALDI-MSI lipidomics data structured as a pandas DataFrame where:

1. **Row Structure**: Each row represents a voxel (3D pixel) from brain slices in the dataset.
2. **Column Structure**:
    - **Lipid Expression Columns**
        - Format: `[Family] [Carbon]:[Double_Bonds]`
        - Example: `PC 36:0` where:
            - `PC`: Lipid family abbreviation
            - `36`: Number of carbon atoms
            - `0`: Number of double bonds
        - Naming must follow this convention for proper lipid family recognition

    - **Metadata Columns**
        - All non-lipid columns
        - Include:
            - Spatial coordinates (x, y, z) in the ABA common coordinate framework
            - Brain slice identifier
            - Additional experimental metadata

Note: The class automatically identifies lipid columns based on their naming pattern, treating all other columns as metadata.

## Class Arguments

- `df`: pandas DataFrame containing lipid expression data and metadata
- `masks_path`: Directory path for storing mask data and generated .lmt files (default: './masks')
- `initial_format`: Input data format ('log', 'exp', 'norm_exp') (default: 'log')
- `final_format`: Desired output format ('log', 'exp', 'norm_exp') (default: 'norm_exp')
- `amplify`: Boolean flag to multiply normalized data by 1000 to mimic gene raw counts (default: True)

## Usage Notes

### Creating .lmt Files
The class generates a lipid family mapping file (`_lipid_families.lmt`) using `create_family_lmt()`:
```python
handler = LBADataHandler(df)
handler.create_family_lmt()
```
The .lmt file maps lipid families to their member lipids in a tab-delimited format:
```
FamilyA    Lipid1    Lipid2
FamilyB    Lipid3    Lipid4
```

### AnnData Integration
Convert processed data to AnnData format using `to_anndata()`:
```python
adata = handler.to_anndata()
```
The resulting AnnData object contains:
- `.X`: Lipid expression matrix (float32)
- `.obs`: Metadata and additional columns
- Automatic handling of data transformations based on specified formats
