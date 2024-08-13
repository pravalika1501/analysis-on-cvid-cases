# analysis-on-cvid-cases
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# Load the dataset (Assuming you have already loaded the dataset into `data` DataFram
data = pd.read_csv('C:/Users/DELL/Downloads/owid-covid-data.csv')


# Convert the 'date' column to datetime format
data['date'] = pd.to_datetime(data['date'])

# Filter data for a specific country (e.g., India)
country_data = data[data['location'] == 'India'].copy()  # Use .copy() to avoid SettingWithCopyWarning

# Fill NaN values with 0 for new cases, deaths, and recoveries
country_data.loc[:, 'new_cases'] = country_data['new_cases'].fillna(0)
country_data.loc[:, 'new_deaths'] = country_data['new_deaths'].fillna(0)
country_data.loc[:, 'new_recoveries'] = (
    country_data['total_cases'] -
    country_data['total_deaths'] -
    country_data['total_cases'].shift(1).fillna(0)
)

# Calculate moving averages
country_data.loc[:, 'cases_MA'] = country_data['new_cases'].rolling(window=7).mean()
country_data.loc[:, 'deaths_MA'] = country_data['new_deaths'].rolling(window=7).mean()
country_data.loc[:, 'recoveries_MA'] = country_data['new_recoveries'].rolling(window=7).mean()

# Visualization (same as before)
plt.figure(figsize=(14, 8))

plt.subplot(3, 1, 1)
plt.plot(country_data['date'], country_data['new_cases'], label='Daily New Cases', color='blue', alpha=0.5)
plt.plot(country_data['date'], country_data['cases_MA'], label='7-Day Moving Average of Cases', color='blue', linewidth=2)
plt.title('COVID-19 Daily New Cases in India')
plt.xlabel('Date')
plt.ylabel('Number of Cases')
plt.legend()

plt.subplot(3, 1, 2)
plt.plot(country_data['date'], country_data['new_deaths'], label='Daily New Deaths', color='red', alpha=0.5)
plt.plot(country_data['date'], country_data['deaths_MA'], label='7-Day Moving Average of Deaths', color='red', linewidth=2)
plt.title('COVID-19 Daily New Deaths in India')
plt.xlabel('Date')
plt.ylabel('Number of Deaths')
plt.legend()

plt.subplot(3, 1, 3)
plt.plot(country_data['date'], country_data['new_recoveries'], label='Daily New Recoveries', color='green', alpha=0.5)
plt.plot(country_data['date'], country_data['recoveries_MA'], label='7-Day Moving Average of Recoveries', color='green', linewidth=2)
plt.title('COVID-19 Daily New Recoveries in India')
plt.xlabel('Date')
plt.ylabel('Number of Recoveries')
plt.legend()

plt.tight_layout()
plt.show()
