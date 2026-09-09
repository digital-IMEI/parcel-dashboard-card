# Parcel Dashboard Card

A compact Home Assistant parcel card for displaying parcel deliveries in a calendar-style layout.

The card is built for the **[Parcel Aggregator](https://github.com/ha-parcel-integrations/ha-parcel-aggregator/)** entities and uses **custom:button-card** for rendering.

## Preview

<p align="center">
  <img src="assets/parcel-dashboard-light.png" alt="Parcel Dashboard Card in Home Assistant light theme" width="49%">
  <img src="assets/parcel-dashboard-dark.png" alt="Parcel Dashboard Card in Home Assistant dark theme" width="49%">
</p>

## Features

- Shows parcels grouped by day
- Carrier-specific colors for PostNL, DHL, DPD, GLS and Dragonfly
- Packages ready for pickup are shown first
- Packages delivered or collected today remain visible until the end of the day
- Packages destined for a pickup point show the pickup location before they are ready for collection
- Distinguishes between `Aangekondigd`, `Onderweg`, `Afhalen`, `Bezorgd` and `Afgehaald`
- Uses Europe/Amsterdam for displayed parcel times
- Dynamic card height based on the number of visible parcels
- No tap actions and no direct carrier sensor dependencies

## Requirements

### Home Assistant entities

The card expects these Parcel Aggregator entities:

```text
sensor.parcel_aggregator_incoming_parcels
sensor.parcel_aggregator_awaiting_pickup
sensor.parcel_aggregator_delivered_parcels
```

### Frontend

Install **custom:button-card** in Home Assistant before using this card.

## Installation

1. Open your Home Assistant dashboard.
2. Add a **Manual** card.
3. Copy the contents of [`parcel-dashboard-card.yaml`](parcel-dashboard-card.yaml).
4. Save the card.

## Parcel status logic

### Aangekondigd

A parcel with a registered/prenotification status is shown as announced. A pickup destination can already be shown at this stage without implying that the parcel is ready for collection.

### Onderweg

A parcel is shown as underway when Parcel Aggregator reports an active transit status.

### Afhalen

A parcel is only shown as ready for pickup when all of the following are true:

```text
delivered != true
status == at_pickup_point
pickup == true
pickup_point is present
```

This intentionally avoids interpreting `pickup: true` by itself as “ready for pickup”. It can simply mean that the destination is a pickup point.

### Bezorgd / Afgehaald

Delivered or collected parcels remain visible for the rest of the delivery day and then disappear from the card.

## Pickup point behaviour

A parcel that is going to a pickup point but has not yet arrived there is shown with a location line such as:

```text
Naar pakketpunt
```

Once it is actually ready for collection, the status changes to `Afhalen` and the parcel is moved to the top of the card.

## Colors

| Carrier / state | Color |
| --- | --- |
| PostNL | `#FF6200` |
| DHL | `#FFCC00` |
| DPD | `#DC0032` |
| GLS | `#1026B3` |
| Dragonfly | `#916BC6` |
| Ready / delivered / collected | `#4caf50` |

## Notes

The card deliberately relies on the aggregated Parcel Aggregator data rather than individual carrier sensors. Tracking barcodes are not displayed or used by the card.
