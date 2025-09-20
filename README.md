                            Program (Python Code)
# Import required libraries
import numpy as np
from scipy import stats

# Example list
data = [12, 15, 20, 15, 30, 25, 15, 10, 20]

# Mean
mean_val = np.mean(data)

# Median
median_val = np.median(data)

# Mode
mode_val = stats.mode(data)

# Variance
variance_val = np.var(data)

# Standard Deviation
std_dev_val = np.std(data)

# Display results
print("Data List:", data)
print("Mean:", mean_val)
print("Median:", median_val)
print("Mode:", mode_val.mode[0], " (Frequency:", mode_val.count[0], ")")
print("Variance:", variance_val)
print("Standard Deviation:", std_dev_val)
