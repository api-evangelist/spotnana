---
name: Create a trip and manage its PNRs
description: Create a Spotnana trip, attach and finalize PNRs, read trip detail, and cancel PNRs.
api: openapi/spotnana-trip-openapi-original.json
operations: [createTrip, createPnrV3, finalizePnr, getTripDetailsV3, cancelPnrV3]
---

# Create a trip and manage its PNRs

Authenticate first (see `spotnana-authenticate.md`) and send `Authorization: Bearer <token>`.

## Steps

1. **Create a trip** — `createTrip` (`POST /v2/trips`). Returns `tripId`. A trip is the container for one or more PNRs (air/hotel/car/rail).
2. **Attach a PNR** — `createPnrV3` (`POST /v3/trips/{tripId}/pnrs`). Air/Hotel booking flows also create PNRs under the trip (see the air/hotel skills).
3. **Finalize** — `finalizePnr` (`POST /v3/trips/{tripId}/pnrs/{pnrId}/finalize`); use `suspendPnr` to hold.
4. **Read trip detail** — `getTripDetailsV3` (`GET /v3/trips/{tripId}/detail`) for the full itinerary; list with `getUserTripSummariesList`.
5. **Approvals** — if policy requires it, process with `processPnrApproval` (`POST /v2/pnrs/{pnrId}/process-approval`).
6. **Cancel** — `cancelPnrV3` (`POST /v3/trips/{tripId}/pnrs/{pnrId}/cancel`) after checking `getCancellationDetails`.

## Rules
- Prefer the v3 trip/PNR operations; v2 equivalents remain for backward compatibility (see `lifecycle/spotnana-lifecycle.yml`).
- No idempotency key: re-read the trip with `getTripDetailsV3` before retrying a failed create/finalize to avoid duplicate PNRs.
- Errors use the `ErrorResponse` envelope; keep the `debugIdentifier` for support.
