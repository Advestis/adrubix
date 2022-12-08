[![doc](https://img.shields.io/badge/-Documentation-blue)](https://advestis.github.io/complex)
[![License: GPL v3](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)

#### Status
<!---
[![pytests](https://github.com/Advestis/adrubix_jmp/actions/workflows/pull-request.yml/badge.svg)](https://github.com/Advestis/adrubix_jmp/actions/workflows/pull-request.yml)
-->
[![push-pypi](https://github.com/Advestis/adrubix_jmp/actions/workflows/push-pypi.yml/badge.svg)](https://github.com/Advestis/adrubix_jmp/actions/workflows/push-pypi.yml)
[![push-doc](https://github.com/Advestis/adrubix_jmp/actions/workflows/push-doc.yml/badge.svg)](https://github.com/Advestis/adrubix_jmp/actions/workflows/push-doc.yml)


![maintained](https://img.shields.io/badge/Maintained%3F-yes-green.svg)
[![issues](https://img.shields.io/github/issues/Advestis/adrubix_jmp.svg)](https://github.com/Advestis/adrubix_jmp/issues)
[![pr](https://img.shields.io/github/issues-pr/Advestis/adrubix_jmp.svg)](https://github.com/Advestis/adrubix_jmp/pulls)


#### Compatibilities
![ubuntu](https://img.shields.io/badge/Ubuntu-supported--tested-success)
![unix](https://img.shields.io/badge/Other%20Unix-supported--untested-yellow)

![python](https://img.shields.io/pypi/pyversions/complex)


##### Contact
[![linkedin](https://img.shields.io/badge/LinkedIn-Advestis-blue)](https://www.linkedin.com/company/advestis/)
[![website](https://img.shields.io/badge/website-Advestis.com-blue)](https://www.advestis.com/)
[![mail](https://img.shields.io/badge/mail-maintainers-blue)](mailto:afedorov@advestis.com)

## AdRubix

Class creating a **RubixHeatmap** object for plotting rather complex, highly customizable heatmaps with metadata.

Example of a heatmap created using AdRubix:

<img src="https://i.ibb.co/zhqNkjb/rubix-heatmap-example.png" alt="heatmap_example" width="1200" />

Three input files (CSV) or pandas DataFrames (in any combination) are expected:

- **Main data** (clusterized by applying, for example, NMTF to raw data).
  
  _Example of rows: biomarkers at different timepoints._
  
  _Example of columns: patients._


- **Metadata for rows**. 
  
  _Example: column 1 = timepoint, column 2 = biomarker._


- **Metadata for columns**.

  _Example: row 1 = score (Y/N), row 2 = treatment (several options), row 3 = cluster no._

The resulting plot is composed of the following elements, all rendered using holoviews.HeatMap().
Their disposition generally looks like :

```
####  [CA]  ####

[RA]  [MP]  [RL]

####  [CL]  ####
```

- `[MP]` _main plot_ (with colorbar on the right)
- `[RA]` _row annotations_ (from metadata for rows)
- `[CA]` _column annotations_ (from metadata for columns) : can be duplicated under the main plot for long DFs
- `[RL]` _row legend_ (RA explained) : optional
- `[CL]` _column legend_ (CA explained) : optional
- `####` white space filler

`plot()` method of the class will save one of the following, or both :
- **HTML plot** : if `save_html` evaluates to True, or in any case if `save_png` evaluates to False
- **PNG image** corresponding to the HTML plot (without toolbar) : if `save_png` evaluates to True

With `plot_save_path` specified, HTML and PNG are saved according to it,
otherwise, HTML only is saved in current working directory to be able to show the plot.


### Main parameters

Default values are bolded, where applicable.

1. **Data input and plot output**
   - `data` (DF) or `data_file` (CSV file name)
   - `metadata_rows` (DF) or `metadata_rows_file` (CSV file name)
   - `metadata_cols` (DF) or `metadata_cols_file` (CSV file name)
   - `data_path` if any of `[...]_file` parameters are used
   - `plot_save_path` = path to HTML file to be saved, _including_ its name
     (PNG image will be saved in the same folder under the exact same name except for the extension)
   - [ optional ] `save_html` = **True**/False or 1/0
   - [ optional ] `save_png` = True/**False** or 1/0


2. **Data scaling and normalization, dataprep**
   - [ optional ] `scale_along` = "**columns**"/"rows" or 0/1 for scaling and capping data along the specified axis
   - [ optional ] `quantile_for_scaling` = quantile for getting rid of outliers, default **0.95**
   - [ optional ] `normalize_along` = "columns"/"rows" or 0/1 for normalizing data along the specified axis :
     `(x - median(x) by column or row) / MAD(x) by column or row`, where `MAD` is median average deviation.
     Default : **do not normalize**.
   - [ optional ] `data_rows_to_drop`, `data_cols_to_drop` = names of rows/columns in main data not intended
     to be plotted. Nonexistent names will be skipped without raising an error.


3. **Colorbar**
   - [ optional ] `colorbar_title` (**no title** by default)
   - [ optional ] `colorbar_height`, `colorbar_location` = "top"/"center"/"**bottom**"
     (always to the right of the main plot)
   - [ optional ] `show_colorbar` = **True**/False


4. **Metadata**
   - [ optional ] `show_metadata_rows` = **True**/False
   - [ optional ] `show_metadata_rows_labels` = True/**False**
   - [ optional ] `show_metadata_cols` = **True**/False
   - [ optional ] `duplicate_metadata_cols` = **True**/False (set automatically to True for DFs longer that 70 rows)


5. **Legends**
   - [ optional ] `show_rows_legend` = **True**/False
   - [ optional ] `show_cols_legend` = **True**/False


6. **Colormaps** (must be known by **holoviews**)
   - [ optional ] `colormap_main` (default "**bwr**" / "**YlOrRd**" for non-negative data)
   - [ optional ] `colormap_metarows` (default "**Glasbey**")
   - [ optional ] `colormap_metacols` (default "**Category20**")


7. **Plot dimensions** (in terms of the main heatmap)
   - [ optional ] `heatmap_width`, `heatmap_height` : either sizes in pixels, or one size and the other "proportional".
     If neither is specified, plot dimensions will be proportional to the DF size (6 screen pixels per row or column).


8. Other
   - [ optional ] `row_labels_for_highlighting` = keywords for identifying row labels to be highlighted
     (in red and italic to the right of the heatmap)


### Example of usage

```python
from adrubix_jmp import RubixHeatmap

hm = RubixHeatmap(
    data_path="/home/user/myproject/data/",
    data_file="main_data.csv",
    metadata_rows_file="meta_rows.csv",
    metadata_cols_file="meta_cols.csv",
    plot_save_path="/home/user/myproject/output/plot.html",
    save_html=False,
    save_png=True,
    colorbar_title="my colorbar",
    colorbar_location="top",
    show_metadata_rows_labels=True,
    show_rows_legend=False,
    rows_legend_onecol=False,
    colormap_main="fire",
    heatmap_width=1500,
    heatmap_height=1000
)
hm.plot()
```