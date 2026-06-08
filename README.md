# Enemalta Tariff Cost Calculator for Home Assistant

A Home Assistant **package** that calculates the running cost of your
electricity consumption against [Enemalta](https://www.enemalta.com.mt/)'s
(Malta) cumulative-band tariffs — **Residential**, **Domestic**, and
**Non-Residential** — including the residential **Eco-Reduction** estimate.

No custom component to install: it's pure YAML (a `utility_meter` plus a
template sensor and a few helpers), so it works on any Home Assistant install
and is easy to inspect and adjust.

> ⚠️ **Information only.** Every effort is made to keep the rates accurate, but
> this is an estimate. Always verify against your ARMS bill. In the event of
> any discrepancy, the *Electricity Connection and Supply Regulations
> (S.L.545.41)* and other applicable laws prevail. See [rate source &
> revision dates](#rate-source).

---

## How it works

Enemalta charges on **cumulative consumption bands that reset each billing
year** — the more you use over the year, the higher the marginal rate on the
top units. This package:

1. Wraps your cumulative energy sensor in a **yearly `utility_meter`** so it
   tracks consumption *within the current billing year* (and resets annually).
2. Walks the bands and sums the cost **marginally**, band by band — exactly how
   Enemalta bills.
3. For Residential primary residences, subtracts an **Eco-Reduction estimate**
   based on registered residents (annualised pro-rata, since the thresholds
   are defined per year).

---

## Requirements

- A sensor that reports **cumulative** energy (a total that only climbs, e.g.
  `total_increasing`). A momentary *power* sensor (W) will **not** work
  directly — see [Wh vs kWh / power vs energy](#wh-vs-kwh--power-vs-energy).
- Units in **kWh**. If your sensor reports **Wh** (common on Tuya/LocalTuya
  plugs), enable the included converter — see below.

---

## Installation

1. Copy `packages/enemalta_tariff.yaml` to your config:
   `<config>/packages/enemalta_tariff.yaml`
   (create the `packages` folder if it doesn't exist).

2. Enable packages in `configuration.yaml` (add under your existing
   `homeassistant:` block, don't create a second one):

   ```yaml
   homeassistant:
     packages: !include_dir_named packages
   ```

3. Point the `utility_meter` at **your** energy sensor. In the package file,
   under `SECTION C`, change:

   ```yaml
   utility_meter:
     enemalta_yearly_energy:
       source: sensor.CHANGE_ME_total_energy_kwh   # <-- your kWh sensor
       cycle: yearly
   ```

4. *(Optional)* If your sensor reads in **Wh**, uncomment `SECTION B` (the
   converter), set its source to your Wh sensor, and point the `utility_meter`
   `source:` at `sensor.energy_kwh_for_enemalta`.

5. **Developer Tools → YAML → Check Configuration**, then **Restart**.

---

## Configuration (helpers)

After restarting, set values under **Settings → Devices & Services → Helpers**:

| Helper | What it does |
| --- | --- |
| **Enemalta Tariff Type** | `Residential` / `Domestic` / `Non-Residential` |
| **Enemalta Registered Residents** | People registered on the ARMS service (drives Eco-Reduction) |
| **Enemalta Primary Residence** | On = eligible for Eco-Reduction |
| **Enemalta VAT Percent** | Only used for Non-Residential (rates there are ex-VAT) |

Add these plus `sensor.enemalta_electricity_cost` to a dashboard
(Entities card) to see the running cost and change settings inline.

---

## Sensor output

`sensor.enemalta_electricity_cost` — estimated cost in € for the billing year
so far. Attributes:

- `tariff_type` — active tariff
- `consumption_kwh_ytd` — kWh counted this billing year
- `gross_cost_eur` — cost before Eco-Reduction / VAT adjustment
- `current_band` — which cumulative band you're currently in
- `note` — VAT / Eco-Reduction caveats

---

## Wh vs kWh / power vs energy

- The bands are in **kWh**. If your sensor is in **Wh**, use the `SECTION B`
  converter (it divides by 1000). A name like "total power" in Wh is usually
  really an *energy* total — that's fine, the converter handles it.
- If your value fluctuates up **and** down, it's instantaneous **power** (W),
  not cumulative energy. You'd first need a
  [Riemann sum integration](https://www.home-assistant.io/integrations/integration/)
  to turn it into energy before feeding this package.

---

## First-run behaviour

The `utility_meter` starts counting from the moment it first runs, so right
after install it reads **0** (not your meter's lifetime total) and grows from
there. The cost will start small and accumulate over the billing year. This is
correct — Enemalta bands are per billing year, not lifetime.

**Reset** at any time (e.g. after fixing a unit mistake) via Developer Tools →
Actions:

```yaml
action: utility_meter.reset
target:
  entity_id: sensor.enemalta_yearly_energy
```

---

## What's not (yet) included

These are uncommon for a typical home and are intentionally left out to keep
things simple — PRs welcome:

- Fixed **annual service charge** (€65 single-phase residential, etc.)
- **Maximum Demand** tariff (three-phase services > 60 A)
- Non-Residential **day/night** and **kVAh** sub-tariff tables

---

## Rate source

Rates transcribed from the official Enemalta pages:

- [Residential](https://www.enemalta.com.mt/13797-2/) — page revised 2026-02-06
- [Domestic](https://www.enemalta.com.mt/domestic-property-services/) — revised 2026-02-06
- [Non-Residential](https://www.enemalta.com.mt/non-residential-property-services/) — revised 2026-04-24

When Enemalta changes the tariffs, update the **RATES SECTION** at the top of
the package file (and the matching values in the template), then bump
`CHANGELOG.md`.

---

## Contributing

Issues and PRs welcome — especially rate updates, the omitted charges above,
and real-world testing across different meter setups. Please note which
Enemalta revision date any rate change corresponds to.

## Licence

[MIT](LICENSE). Provided as-is, with no warranty. Tariff data belongs to
Enemalta plc; this project merely reproduces it for convenience.
