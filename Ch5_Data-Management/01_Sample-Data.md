
## Screenomics 24-Hour Illustrative Text-Based Dataset

This dataset contains 24-hour text-based digital phenotyping data collected from smartphones using the Screenomics platform. It is intended for research and demonstration purposes.  

**Privacy note:** GPS coordinates are rounded to three decimal places, and other columns that could reveal personal identity are not included.

---

### How to Import

After downloading the dataset `DATA999_20250828.parquet` from the [GitHub repository](https://github.com/StanfordScreenomics/Platform/blob/main/Ch5_Data-Management/DATA999_20250828.parquet), you can load it in Python using `pandas` or in R using `arrow`:

#### Python (using pandas)
```python
import pandas as pd

# Load parquet file
data = pd.read_parquet("DATA999_20250828.parquet")

# View the first rows
print(data.head())
```

#### R (using the arrow package)
```r
library(arrow)

# Load parquet file
data <- read_parquet("DATA999_20250828.parquet")

# View the first rows
head(data)
```
