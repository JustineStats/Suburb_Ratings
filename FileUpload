import pandas as pd
import numpy as np
import re
import json

# loading the three files 
df_micro = pd.read_csv('Microburbs.csv')
df_abs = pd.read_csv('ABS Scrapes-csv full with geo.csv')
df_crime = pd.read_csv('crime_rates.csv')

# remove "Crime rate in " from 'Suburb_Full_Name' column + rename subrub name in df_crime to facilitate merging
df_crime['Suburb_Full_Name'] = df_crime['Suburb_Full_Name'].str.replace('Crime rate in ', '')
df_crime.rename(columns={'Suburb_Full_Name': 'suburb_name'}, inplace=True)
# Extracting only the suburb name before the comma #because before the subrub name was 'name, https...'
df_crime['suburb_name'] = df_crime['suburb_name'].str.split(',', expand=True)[0] #0 keep the first string
# displaying the updated 'crime' DataFrame with only suburb names
print(df_crime['suburb_name'])

#MERGING
# left join of df_abs with df_micro on 'suburb_name'
merged_df = df_abs.merge(df_micro, on='suburb_name', how='left')
# Perform a left join of the above result with df_crime on 'suburb_name'
merged_df = merged_df.merge(df_crime, on='suburb_name', how='left')

#REARRANGING
# reorder columns with 'suburb_name' as the first column for clarity 
first_column = 'suburb_name'
merged_df = merged_df[[first_column] + [col for col in merged_df if col != first_column]]
# drop rows where 'suburb_name' is 'NOT FOUND.' 
# 15 'Not found' # replace not found by nan and remove 
merged_df = merged_df[merged_df['suburb_name'] != 'Not found.']

#CHECKING MERGED DATAFRAME
print("Merged LEFT:", merged_df)
print("Merged LEFT descritpive:", merged_df.describe())
unique_suburbs = merged_df['suburb_name'].unique()
print(unique_suburbs)
column_counts = merged_df.count()
print(column_counts)
# Number of common 'suburb_name' entries between 'abs' and 'crime'?
common_entries_crime = merged_df['suburb_name'].notnull().sum()
print(f"Common entries between 'abs' and 'crime': {common_entries_crime}")
# Number of unique suburb names in the 'crime' DataFrame
unique_suburbs_crime = df_crime['suburb_name'].nunique()
# Number of unique suburb names in the 'merged_df'
unique_suburbs_merged = merged_df['suburb_name'].nunique()
# Ensuring number of rows remains the same as in 'abs' DataFrame
merged_df = merged_df.iloc[:len(df_abs)]
print('Number per suburb:',merged_df['suburb_name'].value_counts()) 

# Replacing 'Not found.' with np.nan (which represents a missing or null value);
merged_df = merged_df.apply(lambda x: x.map(lambda y: np.nan if y == 'Not found.' else y))

# Reset the index after removing rows °Important°
merged_df.reset_index(drop=True, inplace=True) 

#'************** Checking numbers - unique - describe - duplicate ******************'
column_counts = merged_df.count()
print("Column counts:", column_counts)

print('abs:',df_abs['suburb_name'].unique())
print('micro:',df_micro['suburb_name'].unique())
print('crime:',df_crime['suburb_name'].unique())
print('merged:',merged_df['suburb_name'].unique()) 

print('abs:',df_abs['suburb_name'].describe()) #15334
print('micro:',df_micro['suburb_name'].describe()) #6344
print('crime:',df_crime['suburb_name'].describe()) #13616
print('merged:',merged_df['suburb_name'].describe()) # 15319 After removing the 15 ‘Not Found.’

print('*******',merged_df.head())
print('*******',merged_df.tail())

# Save the merged DataFrame to CSV and JSON files for safety
merged_df.to_csv('merged_inner_df.csv', index=False)
merged_df.to_json('merged_inner_df.json', orient='records', lines=True)

# Check for duplicates in the entire DataFrame
duplicates = merged_df[merged_df.duplicated()]
print("Number of duplicate rows:", len(duplicates))

# Check for duplicates in the 'suburb_name' column
duplicates = merged_df[merged_df.duplicated(subset=['suburb_name'])]
print("Number of duplicate 'suburb_name' rows:", len(duplicates))
