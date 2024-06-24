# Dataset and Preprocessing

## Data
We used two datasets from CBS regarding the labor market after higher education:
- [Higher education dropouts; labor market position after leaving education](https://opendata.cbs.nl/statline/#/CBS/nl/dataset/85776NED/table?ts=1719254949259)
- [Higher education graduates working as employees; hourly wage after leaving education](https://opendata.cbs.nl/statline/#/CBS/nl/dataset/83815NED/table?ts=1719254998097)

## Filtering
We decided to filter the data directly from StatLine, as doing it through open data sources would take too long to load. For the filtering process, we chose to exclude international students and those with unknown labor market positions. Our focus was on individuals who obtained a degree, excluding those who dropped out or failed to complete their studies. Consequently, we only included individuals with bachelor's and master's degrees.

:::{note}
- Geslacht: totaal
- Persoonskenmerken: geen internationale student
- Perioden: 20.15/'16
- Arbeidsmarkt positie: alles behalve totaal en onbekend
- Uitstromers: wo/hbo bachelor, wo/hbo master
- Studierichting: alles behalve totaal
:::

## Additional calculations
To gain a clearer understanding of the data, we calculated percentages instead of using absolute numbers. This approach is reflected in the two heatmaps.

For example, when we examined the labor market position for each degree. To provide a clearer overview, we calculated the percentage within each degree for each job market position. This makes the distribution per degree much easier to read.