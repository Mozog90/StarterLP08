# LP08 - Defect Report

## Step 2 - Defect Report

| Field | Information |
| --- | --- |
| **Defect ID** | NVB-001 |
| **Title** | Booking form does not enforce the maximum of 12 guests |
| **Date reported** | November 22, 2026 |
| **Reporter** | Student tester |
| **Application or feature** | North Vacation Booking - Add Booking form |
| **Test environment** | North Vacation Booking React + TypeScript application (Vite), Add Booking form and booking state. |
| **Preconditions** | The app and the JSON rental-data service are running. Open the Home page first so rental data loads, then open Add Booking. |
| **Expected result** | A reservation with 13 guests is rejected and the user sees an error because only 1-12 guests are allowed. |
| **Actual result** | The guest input permits values above 12 because it has `min="1"` but no `max`. The submit handler does not check the guest limit before calling `addBooking(formData)`, allowing an out-of-range guest count to be saved. |
| **Severity** | High |
| **Priority** | High |
| **Initial status** | New |
| **Related requirement** | A reservation may include 1-12 guests. |
| **Related test case** | TC-LP08-01: Enter 13 guests and attempt booking |
| **Supporting evidence** | `airbnb-clone-ts/src/pages/AddBooking.tsx` lines 26-29 and 58-65; `airbnb-clone-ts/src/context/BookingContext.tsx` lines 13-15. Relevant source references and a screenshot of the 13-guest booking on the Bookings page are appropriate supporting evidence. |

### Steps to reproduce

1. In `airbnb-clone-ts`, run `npm ci` if the dependencies are not installed.
2. Start the rental data service: `npx json-server --watch db.json --port 3001`.
3. In another terminal, run `npm run dev` and open the displayed Vite URL.
4. Visit **Home** to load the rentals, then select **Add Booking**.
5. Select a rental and enter a valid future date.
6. Enter **13** in Number of Guests.
7. Select **Confirm Booking**.
8. Check whether an error appears. Open **Bookings** and check whether a reservation with 13 guests was added.

### Expected vs. actual

**Expected:** The form rejects 13 guests, explains the 12-guest limit, and does not create the reservation.

**Actual:** The form does not enforce the 12-guest limit. The submit handler passes the guest count to the booking context, which adds the booking without validating it.

### Suggested fix and confirmation testing

Add `max="12"` to the guest input and validate the number in the submit handler so invalid values cannot be saved. After the fix, retry 13 guests and confirm an error appears and no booking is added. Also verify that 1 and 12 guests work, while 0 and 13 do not. Keep the defect open until these checks pass.
