# Forum post draft

**Title:** Enemalta (Malta) electricity tariff cost calculator — running €
cost from your energy sensor

**Body:**
If you're in Malta and on Home Assistant, you've probably wanted to see what
your energy use is actually *costing* against Enemalta's complicated banded tariffs. Those
bands are cumulative per billing year and the marginal rate climbs as you use
more, so a flat "kWh × price" sensor doesn't cut it.

I put together a small **package** (pure YAML — no custom component) that does
it properly:

- Supports **Residential**, **Domestic** and **Non-Residential** tariffs.
- Prices **marginally, band by band**, exactly how Enemalta bills.
- Uses a yearly `utility_meter` so the bands **reset each billing year**.
- Includes the residential **Eco-Reduction** estimate (based on registered
  residents).
- Has an optional Wh→kWh converter for Tuya/LocalTuya plugs that report in Wh.

You point it at your cumulative energy sensor, set a few helpers (tariff type,
residents, etc.), and you get a `sensor.enemalta_electricity_cost` with the
running cost plus useful attributes (current band, gross cost, kWh year-to-date).

**Repo + full instructions:** https://github.com/beta-j/ha-enemalta-tariff

A few honest caveats up front: it's an **estimate** — verify against your ARMS
bill, and the legislation (S.L.545.41) prevails over any rate I've transcribed.
Rates are current as of the Enemalta pages in early/mid 2026; I've isolated the
rate tables at the top of the file and keep a CHANGELOG so updates are easy. I
haven't bothered with the fixed service charge, Maximum Demand, or the
Non-Residential day/night & kVAh tables yet — uncommon for a home, but PRs
welcome.

Feedback and especially testing across different meter setups very welcome —
keen to know if it behaves on sensors other than mine.
