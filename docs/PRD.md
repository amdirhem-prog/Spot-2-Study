# Spot 2 Study — Product Requirements Document (PRD)

**Source documents:** Analyst's Level 1 Specification (Spot 2 Study) and the approved *User Stories List for Spot 2 Study*.
**Status:** Initial scaffolding. The application has not been built yet.

---

## 1. Project Overview

Spot 2 Study is a study location finder for college students who want places that match their study needs. Students can explore locations around **Chico**, including spots on the **Chico State campus** and nearby **off-campus options such as coffee shops, libraries, and parks**.

The application helps students **search and compare** study spots using:

- Distance
- Noise level
- Wi-Fi
- Outlets
- Operating hours
- Seating

Students can also see locations on a map, read study location ratings and reviews, save favorites, and share locations with peers.

The first version focuses on **Chico**.

---

## 2. Target Users

### Primary audience: college students

| Persona | Description | Needs |
| ------- | ----------- | ----- |
| **Maya Rodriguez, Individual Studier** | 20-year-old college student who works at a clothing store and prefers quiet off-campus settings. | Nearby locations; dependable Wi-Fi and outlets; useful noise information; a way to save favorite places. |
| **Ethan Carter, Group Studier** | 22-year-old college student involved in group projects. | Places where classmates can talk; enough seating; operating hours and reviews; an easy way to share a chosen location. |

### Other potential users
- Students looking for a different environment.
- Students who need a quick place to study between classes.
- Remote workers were mentioned as possible users, but **college students remain the main audience**.

### Usage contexts
- **Platform:** a browser-based application usable on **phones and laptops**.
  - On a phone: checking options while walking between classes.
  - On a laptop: comparing multiple places before leaving.
- **Environments:** Chico State campus, student housing, nearby coffee shops, libraries, parks, and other study spaces in Chico.
- **Situations:** studying alone, meeting a group, or planning where to go after class.

---

## 3. Goals

These goals come from what the client emphasized:

1. **Show several recommended spots as soon as the application opens.**
2. **Make campus and off-campus options easy to tell apart.**
3. **Help students choose an appropriate place for the time of day.**
4. **Provide a combined search and filter experience.**
5. **Provide a simple favorites section.**
6. **Show ratings and written reviews from students.**

---

## 4. Functional Requirements

Each functional requirement maps to one of the **11 approved user stories**. Development tasks for each requirement are in [`TASK-LIST.md`](./TASK-LIST.md).

| ID | Requirement | User story |
| -- | ----------- | ---------- |
| FR-01 | **Search** for study locations near campus. Search is combined with filters for distance, noise level, Wi-Fi, outlets, operating hours, and seating. | US-01: As a college student, I want to search for study locations near my campus so that I can find convenient places to complete my schoolwork. |
| FR-02 | **Browse** study locations on campus. Campus and off-campus options are browsed together and are easy to tell apart. | US-02: As a college student, I want to browse study locations on campus so that I can find places to work without leaving school. |
| FR-03 | **Map view** of study locations shown in relation to the student's current location, when location permission is granted. | US-03: As a student unfamiliar with the area, I want to view study locations on a map so that I can understand where they are in relation to my current location. |
| FR-04 | **Recommended locations** shown as soon as the application opens. Recommendations help students choose a place suitable for the time of day and use available information, not live crowd or noise monitoring. | US-04: As a college student, I want to see recommended study locations when I open the application so that I can explore suitable options without having to search first. |
| FR-05 | **Filter by noise level** as part of the combined search and filter experience. | US-05: As a student who prefers quiet environments, I want to filter study locations by noise level so that I can find places where I can concentrate. |
| FR-06 | **Compare** study locations on noise level, amenities (Wi-Fi, outlets, seating), and distance. | US-06: As a college student, I want to compare study locations based on noise levels, amenities, and distance so that I can choose the environment that best fits my needs. |
| FR-07 | **Location details**: operating hours, amenities (Wi-Fi, outlets, seating), and general environment. | US-07: As a college student, I want to view a study location's hours, amenities, and general environment so that I can decide whether it meets my study needs. |
| FR-08 | **View ratings and written reviews** from other students. | US-08: As a college student, I want to view ratings from other students so that I can evaluate how suitable a location is for studying. |
| FR-09 | **Write a review**, including a rating, describing the student's experience at a location. | US-09: As a student who has studied at a location, I want to write a review describing my experience so that other students can learn about the environment before visiting. |
| FR-10 | **Save favorites** and view them in a simple favorites section. | US-10: As a college student, I want to save study locations to my favorites so that I can easily return to places I enjoyed studying. |
| FR-11 | **Share** a study location through a link or messaging application. | US-11: As a student working with classmates, I want to share a study location through a link or messaging application so that others can easily find our meeting place. |

### Out of scope (rejected user stories)
The 18 rejected user stories in the User Stories List are **not** separate requirements. Where the Analyst's rejection reason says the behavior is covered by an approved story, it is handled inside that story's requirement above (for example, ratings are given as part of reviews, and favorites include a section for viewing saved places). These are explicitly **not** part of the first version:

- Directions to a location
- Busy and quiet period information
- Uploading photos
- Organizing favorites into custom lists
- Suggesting new locations
- Reporting incorrect or outdated information

---

## 5. Proposed Technical Architecture

### Technology stack (from the specification)

| Layer | Technology | Repository folder |
| ----- | ---------- | ----------------- |
| Client | **React** with **Vite** | `/client` |
| API | **Express** with **Node.js** | `/api` |
| Database | **Supabase** | `/supabase` (SQL migrations and seed files) |
| Documentation | Markdown / OpenAPI | `/docs` |

### High-level structure

```
┌──────────────────────────────┐
│  Browser (phone or laptop)   │
│  React client (Vite)         │
│  - Search & filters          │
│  - Map view                  │
│  - Recommendations           │
│  - Details, reviews          │
│  - Favorites, share          │
└──────────────┬───────────────┘
               │ HTTP / JSON
┌──────────────▼───────────────┐
│  Express API (Node.js)       │
│  - Locations, search/filter  │
│  - Recommendations           │
│  - Reviews & ratings         │
│  - Favorites                 │
└──────────────┬───────────────┘
               │ Supabase client
┌──────────────▼───────────────┐
│  Supabase (PostgreSQL)       │
│  - Study locations           │
│  - Reviews (with ratings)    │
│  - Favorites                 │
└──────────────────────────────┘
```

- The **client** runs in the browser. It requests data from the API and shows locations as lists, on a map, in comparisons, and on detail pages. If the student allows location access, the client uses the browser's location to show distance. Otherwise, the student can still browse.
- The **API** is the only layer that talks to Supabase. It provides endpoints for locations, search and filtering, recommendations, reviews, and favorites. The API contract will be documented in `/docs` as an OpenAPI file.
- **Supabase** stores the study location data (including campus/off-campus designation, noise level, Wi-Fi, outlets, operating hours, seating, and general environment), student reviews with ratings, and favorites. Schema changes and seed data are kept as SQL files in `/supabase`.
- Secrets such as Supabase keys are kept in `.env` files, which are ignored by Git.

---

## 6. Constraints and Operating Assumptions

- **Geographic scope:** the first version covers **Chico** only.
- **Platform:** browser-based, usable on phones and laptops.
- **Connectivity:** an internet connection is required to load locations, map information, reviews, and recommendations.
- **Location permission:** location permission can help show distance, but students must be able to **browse without sharing their precise location**.
- **Current conditions:** recommendations about current conditions use **available information**, not live crowd or noise monitoring.
- **Unresolved data question:** the source, freshness, and reliability of crowd and noise information are still unresolved.
- **Technology:** React with Vite (client), Express with Node.js (API), and Supabase (database).
- **Scope control:** only the 11 approved user stories are in scope. Rejected user stories must not be treated as approved requirements.

---

## 7. Open Questions

1. How are students identified when they write reviews (US-09) and save favorites (US-10)?
2. What noise level categories will be used, and where will noise information come from? (The specification lists the source, freshness, and reliability of crowd and noise information as unresolved.)
3. Exactly how should recommendations be chosen beyond suitability for the time of day and available information?
