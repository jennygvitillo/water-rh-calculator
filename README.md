# Water vapor saturation pressure & RH% calculator

A simple browser tool to calculate water vapor saturation pressure (p_sat) and relative humidity (RH%), based on the IAPWS formulation (Wagner & Pruss, 1993). Includes a batch mode to generate isobaric RH% tables (constant water vapor partial pressure across a temperature range) with CSV export.

## Quick start

No installation needed — this is a single, self-contained HTML file.

1. Download `water_psat_rh_calculator_jv.html` from this repository
2. Double-click it, or open it with any web browser (Chrome, Firefox, Safari, Edge)
3. That's it — everything runs locally in your browser, no internet connection needed after the first load

Or, if this repository is hosted via GitHub Pages, just open: `https://jennygvitillo.github.io/water-rh-calculator/`

## What it does

- **p_sat(T)**: enter a temperature (°C or K), get the water saturation vapor pressure
- **RH%**: enter a temperature and a water vapor partial pressure (mbar, kPa, RH% at 25°C, or dew/frost point), get the relative humidity at that temperature
- **Batch / CSV**: generate a full table over a temperature range at constant water vapor partial pressure (isobaric conditions), and export it as a CSV file

## Formula

Saturation pressure is calculated with the IAPWS equation for the vapor-liquid saturation curve of water:

Wagner, W.; Pruß, A. *J. Phys. Chem. Ref. Data* **1993**, *22*, 783–787.

Valid between 0.01 °C and 373.9 °C. Below 0 °C, dew/frost point inputs use the Buck (1996) ice saturation formula instead.

## Citation

If you use this tool in a publication, please cite it as:

> Jenny G. Vitillo, jennygvitillo/water-rh-calculator (GitHub repository), 2026. DOI: https://doi.org/10.5281/zenodo.22843285

## License

MIT — see [LICENSE](LICENSE) for details.

## Author

Jenny G. Vitillo (University of Insubria), developed with the assistance of Claude (Anthropic).
