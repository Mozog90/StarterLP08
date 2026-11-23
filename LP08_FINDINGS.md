# LP08 - Defect Management Findings

## Findings overview

I compared the North Vacation Booking requirements with the booking form, data model, and reservation logic. The issues below identify where the current implementation does not meet the stated requirements. The starter application code was left unchanged.

## Step 1 - Classify the Findings

| Finding | Classification | Explanation and evidence |
| --- | --- | --- |
| F-01: A reservation can have more than 12 guests | Product defect | The guest input only has `min="1"`; there is no `max="12"`. `handleSubmit` saves the form without checking the guest count (`src/pages/AddBooking.tsx`, lines 26-29 and 58-65). This conflicts with the 1-12 guest requirement. |
| F-02: No checkout date or number of nights | Product defect | The form only accepts a single `date`. There is no checkout input or duration calculation in `AddBooking.tsx`, and the `Booking` type only has `rental`, `date`, and `guests` (`src/types/index.ts`). The application therefore cannot enforce 1-31 nights or check that check-in comes before checkout. |
| F-03: No check for available nights | Product defect | The submit handler adds a booking directly to React state (`AddBooking.tsx`, lines 26-29; `BookingContext.tsx`, lines 13-15). There is no availability lookup or overlap check in the supplied booking flow. The available-night requirement is not enforced in the booking flow. |
| F-04: The $50 cleaning fee is missing | Product defect | The booking form, `Booking` type, and Bookings page do not calculate or display a cleaning fee. The booking record stores only rental, date, and guests. This does not implement the required one-time $50 fee (`AddBooking.tsx`, `src/types/index.ts`, `src/pages/Bookings.tsx`). |
| F-05: A blank rental can be submitted | Product defect | `formData.rental` starts as an empty string, the rental dropdown has no `required` attribute, and `handleSubmit` doesn't validate the rental before adding a booking (`AddBooking.tsx`, lines 7-11 and 26-43). A blank rental is invalid information, but the application does not show an error for it. This is especially relevant if someone opens the Add Booking page directly before the Home page loads the rental list. |

These findings are not feature requests: each relates to an existing written requirement. I also checked whether the problems could just be incorrect test expectations. The expectations come directly from the instructor's requirements, so they are not test-case mistakes.

## Step 3 - Severity and Priority

| Defect | Severity | Priority | Why |
| --- | --- | --- | --- |
| F-01: Guest limit not enforced | High | High | It lets users submit a reservation that breaks an explicit limit. This should be fixed before release. |
| F-02: Missing checkout and duration | High | High | A major part of creating a reservation is missing, so the required stay rules cannot work. |
| F-03: Missing availability check | High | High | Without checking availability, the booking flow cannot prevent conflicting requests for the same nights. |
| F-04: Missing cleaning fee | Medium | High | The booking can be created, but the required price is incomplete. That affects what a customer expects to pay. |
| F-05: Blank rental accepted | High | High | The app can store a reservation without a property, making the record unusable for a real booking. |

**Severity** is the impact of the problem. **Priority** is how quickly it needs to be fixed. In this case, I would address the missing stay and availability checks before release, then fix the other invalid-booking and pricing problems.

## Follow-up and process improvement

The booking form should validate required values before saving. The data model also needs check-in and checkout dates and enough availability information to prevent overlapping bookings. I'd add automated boundary tests for 0, 1, 12, and 13 guests; 0, 1, 31, and 32 nights; and an overlapping reservation. I would also test the fee once, not once per night, and verify that invalid information produces a visible error.

