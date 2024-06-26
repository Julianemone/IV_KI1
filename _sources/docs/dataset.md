# Dataset and Preprocessing
---
Student names: *Dani dos Santos Bulhão, Casper Knijnenburg, Cayran Maat and Julia de Vries*

Team number: M8

## Data
We used two datasets from CBS regarding the labor market after higher education:
- [Higher education dropouts; labor market position after leaving education](https://opendata.cbs.nl/statline/#/CBS/nl/dataset/85776NED/table?ts=1719254949259)
- [Higher education graduates working as employees; hourly wage after leaving education](https://opendata.cbs.nl/statline/#/CBS/nl/dataset/83815NED/table?ts=1719254998097)

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

The variables we focus on in our data story are: `Uitstromers ho met en zonder diploma`, `Geslacht`, `Studierichting`, `Arbeidsmarktpositie` and `Uurloon werknemers na verlaten ho (euro)`.


## Filtering
We decided to filter the data directly from StatLine, as doing it through CBS open data would take very long to load. For the filtering process, we chose to exclude international students and those with unknown labor market positions. Our focus was on individuals who obtained a degree, excluding those who dropped out or failed to complete their studies. Consequently, we only included individuals with bachelor's and master's degrees.

<b>Filters:</b>
- `Geslacht`: Mannen, Vrouwen, Totaal
- `Persoonskenmerken`: Geen internationale student
- `Perioden`: 2015/'16
- `Arbeidsmarktpositie`: All except total and unknown
- `Uitstromers ho met en zonder diploma`: Wo/hbo bachelor, wo/hbo master
- `Studierichting`: All except total

```sh
# Load datasets
arbeidsmarkt = pd.read_csv("../data/arbeidsmarkt.csv", sep=';')
uurloon = pd.read_csv("../data/uurloon.csv", sep=';')
uurloongem = pd.read_csv("uurloongem.csv", sep=';') # Seperate for easy access

# Converting numbers in string format to numeric format
uurloon['Uurloon werknemers na verlaten ho (euro)']  = pd.to_numeric(uurloon['Uurloon werknemers na verlaten ho (euro)'] , errors='coerce')
arbeidsmarkt['Uitstromers ho (aantal)']  = pd.to_numeric(arbeidsmarkt['Uitstromers ho (aantal)'] , errors='coerce')

# Changing zeroes to non applicable
arbeidsmarkt.fillna(0, inplace = True)
```

## Processing
To gain a clearer understanding of the data, we calculated percentages instead of using absolute numbers. This approach is reflected in the two heatmaps and the linechart.

For example, when we examined the labor market position for each degree. To provide a clearer overview, we calculated the percentage within each degree for each job market position. This makes the distribution per degree much easier to read.

We also aggregated and merged the data in different ways to be able to visualize differents aspects as well as to be able to calculate percentages.

```sh
# Benchmark to be able to get general counts for students per degree and per degree subject (this is the same for every year)
peilmoment1 = arbeidsmarkt.loc[arbeidsmarkt['Peilmoment'] == 'Direct na verlaten onderwijs']

# Grouping to get totals per degree
total_counts_niveau = peilmoment1.groupby('Uitstromers ho met en zonder diploma')['Uitstromers ho (aantal)'].sum().reset_index()
total_counts_niveau.rename(columns={'Uitstromers ho (aantal)': 'Total degree level'}, inplace=True)

# Grouping to get totals per degree subject
total_counts = peilmoment1.groupby('Studierichting')['Uitstromers ho (aantal)'].sum().reset_index()
total_counts.rename(columns={'Uitstromers ho (aantal)': 'Total studierichting'}, inplace=True)

# Merge totals with data
arbeidsmarkt = arbeidsmarkt.merge(total_counts_niveau, on='Uitstromers ho met en zonder diploma')
arbeidsmarkt = arbeidsmarkt.merge(total_counts, on='Studierichting')

# Calculate percentages based on totals and merge
arbeidsmarkt['Percentage studierichting'] = arbeidsmarkt['Uitstromers ho (aantal)'] / arbeidsmarkt['Total studierichting'] * 100
arbeidsmarkt['Percentage degree level'] = arbeidsmarkt['Uitstromers ho (aantal)'] / arbeidsmarkt['Total degree level'] * 100

# Create seperate table per year
arbeidsmarktp0 = arbeidsmarkt.loc[arbeidsmarkt['Peilmoment'] == 'Direct na verlaten onderwijs']
arbeidsmarktp1 = arbeidsmarkt.loc[arbeidsmarkt['Peilmoment'] == '1 jaar na verlaten onderwijs']
arbeidsmarktp2 = arbeidsmarkt.loc[arbeidsmarkt['Peilmoment'] == '2 jaar na verlaten onderwijs']
arbeidsmarktp3 = arbeidsmarkt.loc[arbeidsmarkt['Peilmoment'] == '3 jaar na verlaten onderwijs']
arbeidsmarktp4 = arbeidsmarkt.loc[arbeidsmarkt['Peilmoment'] == '4 jaar na verlaten onderwijs']
arbeidsmarktp5 = arbeidsmarkt.loc[arbeidsmarkt['Peilmoment'] == '5 jaar na verlaten onderwijs']
```