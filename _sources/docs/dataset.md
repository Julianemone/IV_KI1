# Dataset and Preprocessing
---
Authors: *Dani dos Santos Bulhão, Casper Knijnenburg, Cayran Maat and Julia de Vries*

Team number: M8

## Data
We used two datasets from CBS regarding the labour market after higher education:
- [Higher education dropouts; labour market position after leaving education](https://opendata.cbs.nl/statline/#/CBS/nl/dataset/85776NED/table?ts=1719254949259)
    - Before filtering: 4 729 725 rows, 8 columns
    - After filtering: 2400 rows, 8 columns 
- [Higher education graduates working as employees; hourly wage after leaving education](https://opendata.cbs.nl/statline/#/CBS/nl/dataset/83815NED/table?ts=1719254998097)
    - Before filtering: 1 351 350 rows, 7 columns
    - After filtering: 744  rows, 7 columns

## Variable descriptions
- `Geslacht`: Discrete, nominal
- `Persoonskenmerken`: Discrete, nominal
- `Uitstromers ho met en zonder diploma`: Discrete, ordinal
- `Arbeidsmarktpositie`: Discrete, ordinal
- `Studierichting`: Discrete, nominal
- `Peilmoment`: Discrete, interval
- `Perioden`: Discrete, interval
- `Bedrijfstakken (SBI 2008)`: Discrete, nominal
- `Uurloon werknemers na verlaten ho (euro)`: Discrete, ratio

The variables we focus on in our data story are: `Uitstromers ho met en zonder diploma`, `Geslacht`, `Studierichting`, `Arbeidsmarktpositie` and `Uurloon werknemers na verlaten ho (euro)`. The 'Uurloon' dataset we split into three different filtered csv files to be able to access efficiently.


## Cleaning
We sourced the data directly from StatLine due to quicker access compared to CBS open data, which can be slower to load. During the filtering process, we excluded international students and those with unknown labour market positions. Our analysis focused exclusively on graduates who successfully completed their studies, specifically bachelor's and master's degree holders.

<b>Filters applied when possible:</b>
- `Geslacht`: Mannen, Vrouwen, Totaal
- `Persoonskenmerken`: Geen internationale student
- `Perioden`: 2015/'16
- `Peilmoment`: All except '10 jaar na verlaten onderwijs'
- `Arbeidsmarktpositie`: All except total and unknown
- `Uitstromers ho met en zonder diploma`: Wo/hbo bachelor, wo/hbo master
- `Studierichting`: All except total


<b>Code example:</b>

```sh
# Load datasets
arbeidsmarkt = pd.read_csv("../data/arbeidsmarkt.csv", sep=';')
uurloon = pd.read_csv("../data/uurloon.csv", sep=';')
uurloongem = pd.read_csv("uurloongem.csv", sep=';') # Seperate for easy access
uurloongemstudie = pd.read_csv('../data/uurloongemstudie.csv', sep = ';')

# Converting numbers in string format to numeric format
uurloon['Uurloon werknemers na verlaten ho (euro)']  = pd.to_numeric(uurloon['Uurloon werknemers na verlaten ho (euro)'] , errors='coerce')
arbeidsmarkt['Uitstromers ho (aantal)']  = pd.to_numeric(arbeidsmarkt['Uitstromers ho (aantal)'] , errors='coerce')

# Changing zeroes to non applicable
arbeidsmarkt.fillna(0, inplace = True)
```

## Processing
To gain a clearer understanding of the data, we calculated percentages instead of using absolute numbers. This approach is reflected in the two heatmaps and the linechart.

We also aggregated and merged the data in different ways to be able to visualise different aspects as well as to be able to calculate percentages.

<b>Code example:</b>

```sh
# Benchmark to be able to get general counts for students per degree and per degree subject (this is the same for every year)
peilmoment1 = arbeidsmarkt.loc[arbeidsmarkt['Peilmoment'] == 'Direct na verlaten onderwijs']

# Grouping to get totals per degree
total_counts_niveau = peilmoment1.groupby('Uitstromers ho met en zonder diploma')['Uitstromers ho (aantal)'].sum().reset_index()
total_counts_niveau.rename(columns={'Uitstromers ho (aantal)': 'Total degree level'}, inplace=True)

# Merge totals with data
arbeidsmarkt = arbeidsmarkt.merge(total_counts_niveau, on='Uitstromers ho met en zonder diploma')

# Calculate percentages based on totals and merge
arbeidsmarkt['Percentage degree level'] = arbeidsmarkt['Uitstromers ho (aantal)'] / arbeidsmarkt['Total degree level'] * 100

# Create seperate table per year to create seperate traces
arbeidsmarktp0 = arbeidsmarkt.loc[arbeidsmarkt['Peilmoment'] == 'Direct na verlaten onderwijs']
arbeidsmarktp1 = arbeidsmarkt.loc[arbeidsmarkt['Peilmoment'] == '1 jaar na verlaten onderwijs']
arbeidsmarktp2 = arbeidsmarkt.loc[arbeidsmarkt['Peilmoment'] == '2 jaar na verlaten onderwijs']
arbeidsmarktp3 = arbeidsmarkt.loc[arbeidsmarkt['Peilmoment'] == '3 jaar na verlaten onderwijs']
arbeidsmarktp4 = arbeidsmarkt.loc[arbeidsmarkt['Peilmoment'] == '4 jaar na verlaten onderwijs']
arbeidsmarktp5 = arbeidsmarkt.loc[arbeidsmarkt['Peilmoment'] == '5 jaar na verlaten onderwijs']
```