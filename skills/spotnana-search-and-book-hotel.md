---
name: Search and book a hotel
description: Shop, price-check, and book a hotel on the Spotnana Hotel API, then cancel or modify.
api: openapi/spotnana-hotel-openapi-original.json
operations: [hotelSearch, hotelDetails, hotelPriceCheck, hotelCreatePnr, hotelCancelPnr, hotelModifyBooking]
---

# Search and book a hotel

Authenticate first (see `spotnana-authenticate.md`) and send `Authorization: Bearer <token>`.

## Steps

1. **Search hotels** — `hotelSearch` (`POST /v2/hotel/search`). Optionally resolve properties/chains with `hotelSupplierAutocomplete` (`GET /v2/hotel-supplier-autocomplete`).
2. **Get property + rate detail** — `hotelDetails` (`POST /v2/hotel/details`).
3. **Price-check the selected rate** — `hotelPriceCheck` (`POST /v2/hotel/price-check`) to confirm the current price/refundability before booking. Add extras with `hotelAncillaries`.
4. **Book** — `hotelCreatePnr` (`POST /v2/hotel/create-pnr`) to create the hotel PNR under a trip.
5. **Modify** — `hotelModifyBooking` (`POST /v2/hotel/pnrs/{pnrId}/modify-book`) after `hotelValidateRebooking` / `hotelModifyDetails`.
6. **Cancel** — `hotelCancelPnr` (`POST /v2/hotel/pnrs/{pnrId}/cancel-pnr`).

## Rules
- Always `hotelPriceCheck` immediately before `hotelCreatePnr` — rates and refundability can change.
- No idempotency key: re-read PNR state before retrying a create/modify that failed mid-flight.
- Errors use the `ErrorResponse` envelope (`debugIdentifier` + `errorMessages[].errorCode`).
