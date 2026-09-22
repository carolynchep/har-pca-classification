# Human activity recognition with PCA

**STAT 6348 final project** · Carolyne Chepkemboi, Jadon Guthrie, and Willow Teaney

Can principal component analysis make human activity classification faster while preserving useful accuracy? We compared linear discriminant analysis (LDA) on all 561 sensor features with LDA on principal components from the [UCI Human Activity Recognition Using Smartphones dataset](https://archive.ics.uci.edu/dataset/240/human+activity+recognition+using+smartphones). The dataset contains six activities, 7,352 training observations, and 2,947 held-out test observations.

## Results at a glance

| LDA model | Input dimensions | Held-out accuracy | Fit and predict time* |
| --- | ---: | ---: | ---: |
| Standardized features | 561 | 96.2% | ~7.6 s |
| PCA scores (95% training variance retained) | 102 | 92.9% | ~0.3 s |

The PCA model uses about 82% fewer dimensions and loses 3.3 percentage points of test accuracy. Its measured LDA fit and prediction step was about 96% faster on the project hardware. **The timing excludes PCA fitting and transformation**, so it is not an end-to-end deployment speedup. Sitting versus standing remained a difficult distinction.

## Approach

1. Explore activity balance, feature distributions, and correlation among sensor features.
2. Center and scale using **training-set statistics only**; apply those same values to the test set.
3. Fit baseline LDA on the standardized features and evaluate the held-out test set.
4. Fit PCA on training data, retain the components explaining at least 95% of training variance, transform both splits, and fit a second LDA model.
5. Compare confusion matrices, class metrics, accuracy, and model fit/prediction time. The notebook also contains an exploratory k-nearest neighbors (kNN) sweep.

## Files

- [`human_activity_recognition_pca.Rmd`](human_activity_recognition_pca.Rmd): R Markdown analysis, plots, and model comparisons.
- [`STAT_6348_Final_Project.pptx`](presentation/Final_Project Presentation.pptx): original class presentation.

## Reproduce the analysis

1. Download the dataset from [UCI](https://archive.ics.uci.edu/dataset/240/human+activity+recognition+using+smartphones) and unzip it under `data/` so that `data/UCI HAR Dataset/train/X_train.txt` and `data/UCI HAR Dataset/test/X_test.txt` exist. The dataset is excluded from Git.
2. Install R and the packages `rmarkdown`, `ggplot2`, `dplyr`, `FactoMineR`, `factoextra`, `class`, `caret`, `plotly`, `knitr`, and `MASS`. For example:

   ```r
   install.packages(c("rmarkdown", "ggplot2", "dplyr", "FactoMineR",
                      "factoextra", "class", "caret", "plotly", "knitr", "MASS"))
   ```

3. From the repository root, render the notebook:

   ```r
   rmarkdown::render("analysis/har_pca_classification.Rmd",
                     output_file = "har_pca_classification.html",
                     output_dir = ".", knit_root_dir = getwd())
   ```

The original study reported the numbers above; runtime depends on the machine and package versions.

## Evaluation notes

The UCI split separates participants between training and test data. Preprocessing and PCA use training observations only. The kNN section of the original class analysis sweeps hyperparameters **against test accuracy**; treat it as exploratory, since choosing settings on that test set makes its best score optimistic. For a new kNN estimate, select settings with subject-disjoint validation data from the training participants, then evaluate the held-out test set once.

## Data and credit

Dataset: Anguita, D., Ghio, A., Oneto, L., Parra, X., & Reyes-Ortiz, J. L. (2013), *A Public Domain Dataset for Human Activity Recognition Using Smartphones*, ESANN. [UCI dataset page](https://archive.ics.uci.edu/dataset/240/human+activity+recognition+using+smartphones). This was a group course project by Carolyne Chepkemboi, Jadon Guthrie, and Willow Teaney.
