# Spot 2 Study — Development Task List

This task list breaks the **11 approved user stories** from the Analyst's *User Stories List for Spot 2 Study* into development tasks. It follows the Analyst's *Level 1 Specification*, summarized in [`PRD.md`](./PRD.md). Each task lists a description, the user story it belongs to, and testable acceptance criteria.

Rules for this list:

- Only the 11 approved user stories are included. Rejected user stories are listed at the end for reference and are **not** separate features.
- Location attributes come from the specification: distance, noise level, Wi-Fi, outlets, operating hours, and seating, plus campus/off-campus designation and general environment.
- Unresolved items are listed under [Open Questions](#open-questions-for-the-analyst). They are not turned into tasks until the Analyst answers.

**Task ID format:** `T-<story>.<n>`. For example, `T-05.2` is the second task for User Story 5.

**Applies to every UI task:** the screen must be usable at both phone and laptop browser widths, because the specification calls for a browser-based application usable on phones and laptops.

---

## User Story Index

| ID    | Approved user story (summary)                                     |
| ----- | ----------------------------------------------------------------- |
| US-01 | Search for study locations near my campus                         |
| US-02 | Browse study locations on campus                                  |
| US-03 | View study locations on a map relative to my current location     |
| US-04 | See recommended study locations when I open the application       |
| US-05 | Filter study locations by noise level                             |
| US-06 | Compare study locations by noise level, amenities, and distance   |
| US-07 | View a study location's hours, amenities, and general environment |
| US-08 | View ratings from other students                                  |
| US-09 | Write a review describing my experience                           |
| US-10 | Save study locations to my favorites                              |
| US-11 | Share a study location through a link or messaging application    |

---

## US-01 — Search for study locations near campus

> As a college student, I want to search for study locations near my campus so that I can find convenient places to complete my schoolwork.

### T-01.1 — Create the study locations table and seed data
- **Description:** Add a Supabase SQL migration for a `locations` table. Each row stores the location's name, address, latitude/longitude, campus or off-campus designation, noise level, Wi-Fi availability, outlet availability, operating hours, seating information, and general environment description. Add a seed file with Chico study locations: spots on the Chico State campus and off-campus coffee shops, libraries, and parks.
- **User story:** US-01 (also supports US-02 through US-11)
- **Acceptance criteria:**
  - [ ] Running the migration on an empty Supabase database creates the `locations` table without errors.
  - [ ] Running the seed file inserts both campus and off-campus Chico locations.
  - [ ] Every seeded row has a name, address, coordinates, campus/off-campus designation, noise level, Wi-Fi, outlets, operating hours, and seating values.
  - [ ] The migration and seed files are stored in `/supabase/`.

### T-01.2 — Search and filter API endpoint
- **Description:** Add an Express endpoint that returns study locations matching a search term. It also accepts optional filters for Wi-Fi, outlets, seating, and open at the current time (operating hours), as part of the specification's combined search and filter experience. When the client sends the student's coordinates, each result includes its distance. Results can be ordered nearest first and filtered to a selected maximum distance, as the specification lists distance as a search and filter option (see also the reason for Rejected Story 3).
- **User story:** US-01
- **Acceptance criteria:**
  - [ ] A request with a search term returns only locations that match the term.
  - [ ] A request combining a search term with one or more filters returns only locations that match the term **and** every filter.
  - [ ] A request with the "open now" filter excludes locations whose operating hours do not include the current time.
  - [ ] When coordinates are sent, each result includes a distance and results are ordered nearest first. When coordinates are not sent, results are still returned, without distance.
  - [ ] When coordinates and a maximum distance are sent, only locations within that distance are returned. For example, with locations 0.5 and 10 miles away, a 1-mile maximum returns only the 0.5-mile location.
  - [ ] The maximum distance filter can be combined with a search term and the other filters, and results must match all of them.
  - [ ] When a maximum distance is sent without coordinates, the endpoint returns a validation error and doesn't silently ignore the filter.
  - [ ] A zero, negative, or non-numeric maximum distance value returns a validation error.
  - [ ] A request that matches nothing returns an empty list with a success status.

### T-01.3 — Combined search and filter UI
- **Description:** Add one search-and-filter panel to the React client: a search input plus filter controls for Wi-Fi, outlets, seating, open now, and maximum distance. Results appear as a list.
- **User story:** US-01
- **Acceptance criteria:**
  - [ ] Typing a term and submitting shows matching locations.
  - [ ] Changing a filter updates the results without clearing the search term.
  - [ ] Choosing a maximum distance shows only locations within that distance. Clearing it shows all matching locations again.
  - [ ] If the student hasn't granted location permission, the maximum distance control is disabled and the panel explains that location access is needed for it. The other filters keep working.
  - [ ] Each result shows its name, campus/off-campus label, and distance when available.
  - [ ] If nothing matches, the page shows a "no results" message.
  - [ ] Selecting a result opens that location's detail view (T-07.2).

---

## US-02 — Browse study locations on campus

> As a college student, I want to browse study locations on campus so that I can find places to work without leaving school.

### T-02.1 — List locations API endpoint
- **Description:** Add an Express endpoint that returns all study locations, with an optional campus/off-campus filter. Campus and off-campus locations are browsed together, as the specification and the reason for Rejected Story 1 describe.
- **User story:** US-02
- **Acceptance criteria:**
  - [ ] A request with no filter returns both campus and off-campus locations.
  - [ ] A request with the campus filter returns only Chico State campus locations.
  - [ ] Each result includes its campus/off-campus designation.

### T-02.2 — Browse UI with campus/off-campus distinction
- **Description:** Add a browse view that lists study locations, lets the student show only campus locations, and makes campus and off-campus options easy to tell apart.
- **User story:** US-02
- **Acceptance criteria:**
  - [ ] Opening the browse view lists study locations without the student typing a search.
  - [ ] Turning on the campus option shows only campus locations, and turning it off shows all locations again.
  - [ ] Each listed location has a visible campus or off-campus label.
  - [ ] Browsing works when the student has not granted location permission.

---

## US-03 — View study locations on a map

> As a student unfamiliar with the area, I want to view study locations on a map so that I can understand where they are in relation to my current location.

### T-03.1 — Map view with location markers
- **Description:** Add a map view in the React client that places a marker for each study location using its stored coordinates. Campus and off-campus markers look different.
- **User story:** US-03
- **Acceptance criteria:**
  - [ ] The map shows one marker for each study location returned by the API.
  - [ ] Campus markers look visibly different from off-campus markers.
  - [ ] Selecting a marker shows the location's name and a link to its detail view.

### T-03.2 — Show the student's current location on the map
- **Description:** Request the browser's location permission and show the student's current position on the map relative to the study locations.
- **User story:** US-03
- **Acceptance criteria:**
  - [ ] If the student allows location access, a distinct "you are here" marker appears at their position.
  - [ ] If the student denies location access, the map still shows all study location markers and displays a message that the current location is unavailable.

---

## US-04 — Recommended study locations on open

> As a college student, I want to see recommended study locations when I open the application so that I can explore suitable options without having to search first.

### T-04.1 — Recommended locations API endpoint
- **Description:** Add an Express endpoint that returns several recommended study locations. The specification says recommendations help students choose a place suitable for the time of day and use available information (such as stored operating hours), not live crowd or noise monitoring.
- **User story:** US-04
- **Acceptance criteria:**
  - [ ] The endpoint returns more than one location when the database contains open locations.
  - [ ] Locations whose operating hours do not include the current time are not recommended.
  - [ ] The endpoint does not depend on any live crowd or noise data source.

### T-04.2 — Recommendations on the opening screen
- **Description:** Show the recommended study locations on the screen the student sees as soon as the application opens.
- **User story:** US-04
- **Acceptance criteria:**
  - [ ] Opening the application shows several recommended locations without the student typing a search or setting a filter.
  - [ ] Each recommendation shows its name and campus/off-campus label.
  - [ ] Selecting a recommendation opens its detail view.

---

## US-05 — Filter by noise level

> As a student who prefers quiet environments, I want to filter study locations by noise level so that I can find places where I can concentrate.

### T-05.1 — Noise level filter in the API
- **Description:** Add a noise level filter to the search and list endpoints (T-01.2, T-02.1).
- **User story:** US-05
- **Acceptance criteria:**
  - [ ] A request with a noise level filter returns only locations with that noise level.
  - [ ] The noise level filter can be combined with a search term and the other filters.
  - [ ] A request with an invalid noise level value returns a validation error.

### T-05.2 — Noise level filter control
- **Description:** Add a noise level control to the combined search-and-filter panel (T-01.3).
- **User story:** US-05
- **Acceptance criteria:**
  - [ ] Choosing a noise level updates the results so only matching locations are shown.
  - [ ] Clearing the noise level filter shows the unfiltered results again.

---

## US-06 — Compare study locations

> As a college student, I want to compare study locations based on noise levels, amenities, and distance so that I can choose the environment that best fits my needs.

### T-06.1 — Distance calculation
- **Description:** Calculate the distance from the student's current location to each study location. This requires location permission. Students who don't share their location can still use the app.
- **User story:** US-06 (also used by US-01)
- **Acceptance criteria:**
  - [ ] For a known pair of coordinates, the calculated distance matches the expected value within a documented tolerance.
  - [ ] Distance is shown in one consistent unit.
  - [ ] If location permission is not granted, distance shows as unavailable and no error is thrown.

### T-06.2 — Select locations to compare
- **Description:** Let the student choose two or more study locations to compare.
- **User story:** US-06
- **Acceptance criteria:**
  - [ ] The student can add a location to the comparison from the results list or the detail view.
  - [ ] The student can remove a location from the comparison.

### T-06.3 — Side-by-side comparison view
- **Description:** Show the selected locations side by side with their noise level, amenities (Wi-Fi, outlets, seating), and distance.
- **User story:** US-06
- **Acceptance criteria:**
  - [ ] Each selected location appears in its own column or row.
  - [ ] Noise level, Wi-Fi, outlets, seating, and distance are shown for every selected location.
  - [ ] The comparison is readable on both phone and laptop widths.

---

## US-07 — View location hours, amenities, and environment

> As a college student, I want to view a study location's hours, amenities, and general environment so that I can decide whether it meets my study needs.

### T-07.1 — Location detail API endpoint
- **Description:** Add an Express endpoint that returns one study location by id, including its operating hours, amenities (Wi-Fi, outlets, seating), noise level, campus/off-campus designation, and general environment. Seating is part of location details, as the reason for Rejected Story 8 states.
- **User story:** US-07
- **Acceptance criteria:**
  - [ ] A request for an existing id returns that location's operating hours, Wi-Fi, outlets, seating, noise level, campus/off-campus designation, and general environment.
  - [ ] A request for an id that does not exist returns a not-found response.

### T-07.2 — Location detail view
- **Description:** Add a detail page in the React client that shows all of the location information from T-07.1.
- **User story:** US-07
- **Acceptance criteria:**
  - [ ] Opening a location's detail page shows its operating hours, Wi-Fi, outlets, seating, noise level, campus/off-campus label, and general environment.
  - [ ] Each location's detail page has its own URL, which US-11 uses for sharing.

---

## US-08 — View ratings from other students

> As a college student, I want to view ratings from other students so that I can evaluate how suitable a location is for studying.

### T-08.1 — Ratings and reviews retrieval API
- **Description:** Add an Express endpoint that returns a location's student ratings, its average rating, and the written reviews. The specification calls for showing ratings and written reviews, and the reason for Rejected Story 14 says reviews are read alongside ratings.
- **User story:** US-08
- **Acceptance criteria:**
  - [ ] For a location with reviews, the response includes each rating, its review text, and the average rating.
  - [ ] For a location with no reviews, the response returns an empty list and no average.

### T-08.2 — Ratings and reviews on the detail view
- **Description:** Show the average rating, the number of ratings, and each student rating with its written review on the location detail view.
- **User story:** US-08
- **Acceptance criteria:**
  - [ ] The detail view shows the average rating and the number of ratings.
  - [ ] Individual ratings are listed with their written review text.
  - [ ] A location with no ratings shows a "no ratings yet" message.

---

## US-09 — Write a review

> As a student who has studied at a location, I want to write a review describing my experience so that other students can learn about the environment before visiting.

### T-09.1 — Reviews table
- **Description:** Add a Supabase migration for a `reviews` table linked to `locations`. Each review stores a rating and written text, because ratings are given as part of reviews (reason for Rejected Story 12).
- **User story:** US-09 (also supports US-08)
- **Acceptance criteria:**
  - [ ] Running the migration creates the `reviews` table, linked to `locations`.
  - [ ] A review cannot be saved without a location, a rating, and review text.

### T-09.2 — Submit review API endpoint
- **Description:** Add an Express endpoint that accepts a new review (rating and text) for a location.
- **User story:** US-09
- **Acceptance criteria:**
  - [ ] A valid submission is saved and is returned by the T-08.1 endpoint.
  - [ ] A submission missing the rating or text returns a validation error and nothing is saved.
  - [ ] A submission for a location that does not exist returns a not-found response.

### T-09.3 — Review form UI
- **Description:** Add a form on the location detail view where the student enters a rating and writes a review.
- **User story:** US-09
- **Acceptance criteria:**
  - [ ] Submitting a valid review shows it on the location's detail view without a manual page refresh.
  - [ ] Submitting with missing fields shows a validation message, and the review is not posted.

---

## US-10 — Save locations to favorites

> As a college student, I want to save study locations to my favorites so that I can easily return to places I enjoyed studying.

### T-10.1 — Favorites table and API
- **Description:** Add a Supabase migration for a `favorites` table. Add Express endpoints to add a favorite, remove a favorite, and list the student's favorites.
- **User story:** US-10
- **Acceptance criteria:**
  - [ ] Adding a location to favorites saves it, and it appears in the favorites list response.
  - [ ] Removing a favorite removes it from the favorites list response.
  - [ ] Adding the same location twice does not create a duplicate.

### T-10.2 — Save / unsave control
- **Description:** Add a control on the results list and detail view that saves a location to favorites or removes it.
- **User story:** US-10
- **Acceptance criteria:**
  - [ ] Selecting the control on an unsaved location saves it, and the control shows a saved state.
  - [ ] Selecting the control on a saved location removes it, and the control shows an unsaved state.

### T-10.3 — Simple favorites section
- **Description:** Add a simple favorites section that lists the student's saved locations. The specification calls for a simple favorites section, and the reason for Rejected Story 15 says viewing saved places is part of the favorites feature. Custom lists are out of scope (Rejected Story 16).
- **User story:** US-10
- **Acceptance criteria:**
  - [ ] The favorites section lists every location the student has saved, in a single list.
  - [ ] Selecting a favorite opens its detail view.
  - [ ] When nothing is saved, the section shows an empty-state message.

---

## US-11 — Share a study location

> As a student working with classmates, I want to share a study location through a link or messaging application so that others can easily find our meeting place.

### T-11.1 — Copy shareable link
- **Description:** Add a share control on the location detail view that copies the location's detail page URL (T-07.2).
- **User story:** US-11
- **Acceptance criteria:**
  - [ ] Selecting the copy option puts the location's detail URL on the clipboard and shows a confirmation.
  - [ ] Opening the copied URL in a new browser session shows the same location's detail page.

### T-11.2 — Share through a messaging application
- **Description:** On browsers that support it, open the device's native share sheet (Web Share API) so the student can send the location link through a messaging application.
- **User story:** US-11
- **Acceptance criteria:**
  - [ ] On a supported browser, selecting share opens the native share sheet with the location's name and URL filled in.
  - [ ] On an unsupported browser, the copy-link option (T-11.1) is still available.

---

## Open Questions for the Analyst

1. **User identity:** How are students identified when they write reviews (US-09) and save favorites (US-10)?
2. **Noise information (US-05, US-06):** What noise level categories will be used, and where does noise information come from? The specification lists the source, freshness, and reliability of crowd and noise information as unresolved.
3. **Recommendation rules (US-04):** Beyond time of day and available information, how should recommended locations be chosen and ordered?

---

## Rejected User Stories (Not Separate Features)

These stories were rejected by the Analyst and are not built as separate features. Where the specification already covers the behavior, it is handled inside the approved story shown.

| #  | Rejected story (summary)                     | Analyst's reason (summary)                            | Handling in this task list |
| -- | -------------------------------------------- | ----------------------------------------------------- | -------------------------- |
| 1  | Discover off-campus study locations          | Covered by browsing campus and off-campus together    | Inside US-02 |
| 2  | Access directions to a location              | Can be added after main search features work          | Not in scope |
| 3  | Filter by distance                           | Included with search and comparison options           | Inside US-01 / US-06 |
| 4  | Filter by Wi-Fi availability                 | Included as a main search filter                      | Inside US-01 |
| 5  | Find locations with electrical outlets       | Included as a main search filter                      | Inside US-01 |
| 6  | Filter by operating hours                    | Included in details and recommendations               | Inside US-01 / US-04 / US-07 |
| 7  | Find group-friendly seating and space        | Covered by location details and filters               | Inside US-01 / US-07 (seating) |
| 8  | View seating arrangement information         | Already part of location details                      | Inside US-07 |
| 9  | View photos of the environment               | Can be shown with location details                    | Not in scope (not in specification) |
| 10 | View busy and quiet periods                  | No reliable data source yet                           | Not in scope |
| 11 | Time-based recommendations                   | Included in the recommendations feature               | Inside US-04 |
| 12 | Rate my experience (separate story)          | Ratings are part of the review feature                | Inside US-09 |
| 13 | Upload photos of the space                   | Photo storage and moderation not decided              | Not in scope |
| 14 | Read reviews (separate story)                | Reviews are read alongside ratings                    | Inside US-08 |
| 15 | Access saved locations in one section        | Already included in the favorites feature             | Inside US-10 |
| 16 | Organize saved locations into lists          | Client wants a simple favorites section               | Not in scope |
| 17 | Suggest a new location                       | Suggestion and approval process not agreed            | Not in scope |
| 18 | Report incorrect or outdated information     | Reporting and verification process not decided        | Not in scope |
