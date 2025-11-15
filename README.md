# Climate Change – Temperatures and Precipitation

This repository contains my work for **Project 2: Climate Change – Temperatures and Precipitation** (Data 8).  
The project uses historical temperature and precipitation data to explore climate trends, build confidence intervals, and run hypothesis tests in Python.

---

## Overview

- **Environment:** Jupyter Notebook  
- **Language:** Python 3  
- **Libraries:** `datascience` `numpy` `calendar` (and course-provided utilities)  
- **Main notebook:** `project2.ipynb`  

The project is divided into two main parts:

1. **Part 1 – Temperatures:** Focused on temperature changes over time, with special attention to Phoenix, Arizona.  
2. **Part 2 – Drought:** Focused on California precipitation, drought years, and hypothesis testing using permutation tests.

---

## Part 1: Temperatures

### Working with Cities and Regions

In the cities portion, I:

- Wrote **`coordinates_to_region(lat, lon)`** to classify cities into broad U.S. regions (`Northeast`, `Northwest`, `Southeast`, `Southwest`) using latitude/longitude cutoffs.
- Implemented **`distance(lat0, lon0, lat1, lon1)`** to approximate distances between city coordinate pairs using a Euclidean formula in latitude–longitude space.
- Used these functions, along with provided tables, to reason about which cities are closest to a given location and how they cluster by region.

### Phoenix Temperatures and Time Periods

For the Phoenix analysis, I:

- Implemented **`extract_year_from_date(date)`** and **`extract_month_from_date(date)`** to pull out the year as an integer and a formatted month label (e.g., `02 (Feb)`) from date strings.
- Defined **`period(year)`** to label each year as `"Past"`, `"Present"`, or `"Other"` based on ranges:
  - Past: roughly 1900–1960  
  - Present: recent years (2019–2021)
- Created a modified Phoenix table (`phoenix_dup`) that adds a `"Period"` column using `period`, then:
  - Built separate tables for **past** and **present** February temperatures.
  - Used `pivot` and `np.mean` to compute average maximum temperatures by month and period.

### Confidence Intervals and Comparing Past vs. Present

To quantify uncertainty and compare past vs. present:

- Implemented **`make_ci(tbl, level)`**, which:
  - Uses bootstrap resampling (`tbl.sample(with_replacement=True)`) 5000 times.
  - Stores the mean of each resample.
  - Computes lower and upper bounds from percentiles to form a confidence interval at a chosen level (e.g., 99%).
- Used `make_ci` on present-day February temperatures to create a **99% confidence interval** for the mean `tmax`.
- Wrote a loop that:
  - Iterates over each month.
  - Grabs the past average for that month and the present-day distribution.
  - Builds a **99% CI** for the present average.
  - Checks whether the past average falls inside or outside this CI.
  - Prints a colored line summarizing whether each past monthly average is **contained** in the present interval or **NOT contained**.

This analysis helps compare historical vs. recent temperature patterns month by month and see which months show the clearest warming signals.

---

## Part 2: Drought

In the drought portion (California precipitation), I:

- Created **`drought_label(n)`** to classify years as `"drought"` or `"other"` based on specified drought periods (e.g., 2002–2005 and 2012–2020).
- Used this labeling to add a `"Label"` column to a precipitation table, distinguishing drought years from non-drought years.

### Test Statistic and Observed Difference

- Implemented **`test_statistic(t)`**, which:
  - Separates rows where `"Label"` is `"drought"` and `"other"`.
  - Computes the **average precipitation** for each group.
  - Returns the difference:

    \[
    \text{mean precipitation (drought years)} - \text{mean precipitation (other years)}
    \]

- Computed **`observed_statistic`** by applying `test_statistic` to the actual labeled dataset, giving the observed difference in average precipitation between drought and other years.

### Permutation Test (Null Simulation)

To assess whether the observed difference could be due to chance:

- Implemented **`simulate_precipitation_null()`**, which:
  - Randomly permutes the `"Label"` column using `np.random.choice` without replacement.
  - Recombines the shuffled labels with the original precipitation data.
  - Recomputes the test statistic on this shuffled table.
- Ran `simulate_precipitation_null()` many times to:
  - Build an empirical **null distribution** of the test statistic under the assumption that labels (drought vs. other) do not matter.
  - Compare the real observed statistic to this null distribution and reason about the strength of evidence for a true difference.

This permutation test framework provides a way to formally test whether drought years genuinely differ in average precipitation from other years beyond what we’d expect by random assignment.

---

## How to Run the Notebook

1. Open the notebook in Jupyter:

   ```bash
   jupyter notebook project2.ipynb
