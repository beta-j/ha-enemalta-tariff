# Changelog

All notable changes to this project are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/).
Each release notes the **Enemalta tariff revision date** the rates match, so
users can tell at a glance whether a release is current.

## [Unreleased]

## [1.0.0] - 2026-06-08
### Added
- Initial release.
- Residential, Domestic and Non-Residential cumulative-band cost calculation.
- Marginal (band-by-band) pricing via a yearly `utility_meter`.
- Residential Eco-Reduction estimate (annualised pro-rata) for 1-person and
  2+-person households.
- Optional Wh→kWh converter for Tuya/LocalTuya-style sensors.
- Configurable helpers: tariff type, registered residents, primary-residence
  flag, Non-Residential VAT percent.

### Rates
- Residential & Domestic: match Enemalta pages revised **2026-02-06**.
- Non-Residential (kWh): match Enemalta page revised **2026-04-24**.

### Not included
- Fixed annual service charges.
- Maximum Demand tariff (three-phase > 60 A).
- Non-Residential day/night and kVAh sub-tariff tables.

<!--
When updating rates, copy this template:

## [x.y.z] - YYYY-MM-DD
### Changed
- Updated <Residential/Domestic/Non-Residential> band rates to match Enemalta
  page revised YYYY-MM-DD.
-->
