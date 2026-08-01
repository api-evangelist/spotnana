---
name: Search and book a flight
description: Shop, select, checkout, and ticket a flight on the Spotnana Air API, then cancel if needed.
api: openapi/spotnana-air-openapi-original.json
operations: [airSearch, airSelectedItinerary, airFlightCheckout, airInitiateBooking, airCreatePnr, airCancelPnr]
---

# Search and book a flight

Authenticate first (see `spotnana-authenticate.md`) and send `Authorization: Bearer <token>` on every call.

## Steps

1. **Search flights** — `airSearch` (`POST /v2/air/search-flights`). Provide origin, destination, dates, cabin, and traveler. Optionally resolve airport/airline codes with `airAutocomplete` (`GET /v2/air-autocomplete`).
2. **Select an itinerary** — `airSelectedItinerary` (`POST /v2/air/selected-itinerary`) to lock the chosen option and get fare/attribute detail (`airAttributes`).
3. **Checkout** — `airFlightCheckout` (`POST /v2/air/flight-checkout`) to price the selection with traveler + payment context; optionally `airSeatMap` (`POST /v2/air/seat-map`) for seats.
4. **Initiate booking** — `airInitiateBooking` (`POST /v2/air/initiate-booking`).
5. **Create the PNR (ticket)** — `airCreatePnr` (`POST /v2/air/create-pnr`). This produces the airline PNR under a Spotnana trip.
6. **Cancel if required** — `airCancelPnr` (`POST /v2/air/cancel-pnr`); inspect unused credits with `fetchTravelerUnusedCredits`.

## Rules
- Policy checks apply — a booking may require approval (see the Trip/Approval flow).
- No idempotency key: if a create call times out, re-read trip/PNR state before retrying to avoid duplicate bookings.
- Errors use the `ErrorResponse` envelope; `403` indicates the traveler/agent lacks the required RBAC permission.
