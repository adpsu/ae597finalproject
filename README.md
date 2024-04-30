# ae597finalproject
The Final project codes were developed by Abdulrahaman Aljuhani and Adway Das. The project was mentored by Dr. Rebecca Napolitano.


import pandas as pd
import numpy as np

# Assuming ev_df is already loaded and structured correctly

# Function to find MSRP for the same make and model for adjacent years
def get_adjacent_year_avg_msrp(row, df):
    make = row['Make']
    model = row['Model']
    year = row['Year']

    # Fetch MSRP for year before and year after
    prev_year_msrp = df.loc[(df['Make'] == make) & (df['Model'] == model) & (df['Year'] == year - 1), 'MSRP']
    next_year_msrp = df.loc[(df['Make'] == make) & (df['Model'] == model) & (df['Year'] == year + 1), 'MSRP']

    # Ensure both MSRP values are available and not NaN
    if not prev_year_msrp.empty and not np.isnan(prev_year_msrp.values[0]) and not next_year_msrp.empty and not np.isnan(next_year_msrp.values[0]):
        return np.mean([prev_year_msrp.values[0], next_year_msrp.values[0]])
    return np.nan

# Create a copy of the DataFrame to avoid SettingWithCopyWarning
ev_df_copy = ev_df.copy()

# Apply the function only to rows where MSRP is NaN and total_sales is not NaN
ev_df_copy['MSRP'] = ev_df.apply(lambda row: get_adjacent_year_avg_msrp(row, ev_df) if pd.isna(row['MSRP']) and not pd.isna(row['total_sales']) else row['MSRP'], axis=1)

# Optionally, check changes or update the original DataFrame
ev_df.update(ev_df_copy)
