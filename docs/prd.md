# Excursion Rides Product Requirements Document (PRD)

## 1. Goals and Background Context

### 1.1. Goals
* **Simplify Architecture**: Migrate from a multi-service stack to a unified Supabase platform.
* **Improve Developer Experience**: Leverage Supabase's integrated tooling and auto-generated APIs to speed up development.
* **Enhance Security**: Replace custom authentication implementations with Supabase's professionally maintained Auth service.
* **Reduce Technical Debt**: Start with a cleaner architecture to create a more scalable and maintainable long-term foundation.
* **Enable Real-Time Features**: Utilize Supabase's built-in real-time capabilities for features like live tracking and chat.

### 1.2. Background Context
This document outlines the requirements for Excursion Rides, a new ride-sharing application built from scratch on the Supabase platform. This greenfield project will leverage the requirements and feature specifications from a previous iteration to accelerate development. The strategic decision to build fresh on Supabase was made to create a superior, more scalable, and secure foundation for the MVP and beyond, avoiding the complexities identified in the previous stack.

### 1.3. Change Log
| Date | Version | Description | Author |
| :--- | :--- | :--- | :--- |
| 2025-07-11 | 1.0 | Initial draft of the Greenfield PRD for the Supabase build. | John, PM |

---

## 2. Requirements

### 2.1. Functional Requirements (FR)
1.  **FR1:** Users shall sign up for a single, unified account.
2.  **FR2:** An existing user can apply to become a driver. This action will direct them to an external webpage in an in-app browser.
3.  **FR3:** Approved drivers shall be able to add their vehicle and license plate information to their profile.
4.  **FR4:** A newly approved driver must complete a Stripe Connect onboarding process before they can accept rides.
5.  **FR5:** Riders shall be able to add and save a credit card payment method, managed via Stripe. Apple Pay and Google Pay will also be supported.
6.  **FR6:** Riders shall enter a pickup location and a destination to request a ride.
7.  **FR7:** The application will calculate the trip distance using the Google Maps Directions API.
8.  **FR8:** The application will determine all valid, fixed-price fare tiers based on the vehicle type, trip type (one-way/round-trip), and calculated distance.
9.  **FR9:** The rider will be shown all applicable fare tiers, with the cheapest option selected by default.
10. **FR10:** A ride request will be broadcast to the nearest eligible and available drivers. If no driver accepts, the search radius will be expanded for a second attempt before proceeding to the "Notify Me" flow.
11. **FR11:** A driver can accept or ignore a ride request. The request will include the rider's pickup location and the estimated time to pickup.
12. **FR12:** Drivers shall be able to copy the pickup and destination addresses from the active trip screen.
13. **FR13:** A 4-digit verification PIN will be generated and shown to the rider for the duration of the pickup.
14. **FR14:** A driver must successfully enter the rider's PIN to confirm pickup and officially start the trip.
15. **FR15:** Riders and drivers can communicate via a real-time chat interface during an active trip. The interface will include pre-populated "quick reply" options.
16. **FR16:** Riders can cancel a trip at any point before the PIN has been successfully entered by the driver.
17. **FR17:** Drivers can cancel a trip at any point before they have started the route to the pickup location.
18. **FR18:** Upon ride booking, the system will place an authorization hold on the rider's card for the fare amount. Upon trip completion, this authorization will be captured.
19. **FR19:** After the ride, riders will be prompted to add an optional tip for the driver, processed by Stripe.
20. **FR20:** Riders and drivers can provide a mutual 1-5 star rating and an optional comment after each trip.
21. **FR21:** If no drivers are available, a rider can opt to join a "virtual queue" and receive a push notification when a driver is found.
22. **FR22:** Riders can view their past trip history.
23. **FR23:** Drivers can view a summary of their earnings, including fares and tips.
24. **FR24:** The system must trigger payouts of the driver's net earnings on a regular, defined schedule.
25. **FR25:** An administrator can approve a driver by manually updating their status in the system.

### 2.2. Non-Functional Requirements (NFR)
* **NFR1:** All payments, card storage, and tips must be processed by Stripe.
* **NFR2:** All mapping, routing, and distance calculations must use the Google Maps Platform APIs.
* **NFR3:** The application must be designed with a mobile-first approach.
* **NFR4:** All new user accounts created with an email/password must be verified via an email link, handled by Supabase Auth.

---

## 3. User Interface Design Goals
A comprehensive UI/UX Specification document has been created that outlines the detailed vision. Key aspects include:
* **Design System**: shadcn/ui with Tailwind CSS.
* **Core Principles**: Safety & Trust First, Clarity & Simplicity, Seamless & Reliable, Efficiency for Both Roles.
* **Accessibility**: Target compliance with WCAG 2.1 Level AA.

---

## 4. Technical Assumptions

* **Repository Structure**: The project will be developed within a Monorepo.
* **Service Architecture**: The backend will be built using a Serverless Architecture (Supabase Edge Functions / Vercel Functions).
* **Testing Requirements**: Strategy will include Unit/Integration tests (Jest/RTL) and E2E tests (Playwright).
* **Deployment Platform**: The application will be deployed on Vercel.
* **Core Backend**: Supabase will serve as the unified backend platform.
* **Push Notifications**: Firebase Cloud Messaging will be used.

---

## 5. Epic List

1.  **Epic 1: Foundation & Authentication** - Goal: To establish the core project infrastructure, configure the Supabase backend, and implement the complete user authentication system.
2.  **Epic 2: User Management & Profiles** - Goal: To build the systems for managing rider profiles and the full lifecycle for drivers, from application to approval and vehicle setup.
3.  **Epic 3: Ride Booking System** - Goal: To implement the end-to-end ride booking experience, including fare calculation, trip requests, and driver matching logic.
4.  **Epic 4: Payment Integration** - Goal: To integrate Stripe for handling all payment-related features, including adding payment methods, processing ride fares, managing tips, and enabling driver payouts.
5.  **Epic 5: Real-time Features & Notifications** - Goal: To leverage Supabase Realtime and Firebase Cloud Messaging to enable live ride tracking, in-app chat, and push notifications.

---

## 6. Epic Details

### Epic 1: Foundation & Authentication

**Goal:** To establish the core project infrastructure, configure the Supabase backend, and implement the complete user authentication system.

#### Story 1.1: Project Setup & Supabase Configuration
**Goal**: To initialize the monorepo, configure Vercel, and connect the project to our new Supabase instance.
**Acceptance Criteria**:
1.  A new monorepo is created with pnpm workspace configuration and pushed to a new Git repository.
2.  Code quality gates are established, including ESLint, Prettier, and TypeScript, with pre-commit hooks to enforce standards.
3.  A new Vercel project is created and linked to the Git repository to enable CI/CD and preview deployments.
4.  A new Supabase project is created, and its `SUPABASE_URL` and `SUPABASE_ANON_KEY` are securely configured as environment variables in the Vercel project.
5.  The Supabase SDK (`@supabase/supabase-js`) is installed, and a shared, reusable Supabase client is configured within the monorepo.
6.  The testing infrastructure is set up, including Jest/React Testing Library for unit tests and Playwright for E2E tests.
7.  The shadcn/ui component system is initialized and configured with Tailwind CSS.
8.  A simple health check API endpoint (e.g., `/api/health`) is created that successfully queries the Supabase database to confirm a valid connection.
9.  A successful deployment of the initial application shell is made to Vercel's preview environment.

#### Story 1.2: Core Authentication Implementation
**Goal**: To implement user signup, login (email/password and social), and session management using Supabase Auth.
**Acceptance Criteria**:
1.  A sign-up form is created with fields for the user's name, email, and password.
2.  Client-side and server-side validation is implemented to ensure a valid email format and enforce a strong password policy.
3.  A login form is created allowing users to sign in with their email and password.
4.  The signup and login forms include buttons for authentication with Google and Apple providers.
5.  A successful email/password signup calls Supabase's `signUp` method, creating a new user in the `auth.users` table.
6.  A successful social login calls Supabase's `signInWithOAuth` method, correctly configured for the Google and Apple providers.
7.  Upon successful login via any method, a valid session is established by the Supabase client, and the user is redirected to the main application screen.
8.  Route protection is implemented (e.g., via Next.js middleware) that validates the Supabase session, securing all non-public routes.

#### Story 1.3: Email Verification Flow
**Goal**: To configure and implement the full email verification workflow using Supabase's built-in email handling.
**Acceptance Criteria**:
1.  The "Confirm email" setting is enabled within the Supabase project's authentication configuration.
2.  When a user signs up with an email and password, Supabase automatically sends a verification email to the provided address.
3.  The application has a specific page or route to handle the callback from the verification email link.
4.  When a user clicks the verification link, their `email_confirmed_at` status in the Supabase `auth.users` table is automatically populated.
5.  An unverified user who is logged into the application is shown a persistent, non-dismissible banner prompting them to verify their email.
6.  Core application functionality that requires a trusted user (e.g., booking a ride) is disabled until the user's email has been verified.

### Epic 2: User Management & Profiles

**Goal:** To build the systems for managing rider profiles and the full lifecycle for drivers, from application to approval and vehicle setup.

#### Story 2.1: Rider Profile Management
**Goal**: To allow a logged-in user to view and manage their basic profile information.
**Acceptance Criteria**:
1.  An authenticated user can navigate to a dedicated "Profile" page.
2.  The profile page displays the user's current name and email address as stored in the database. The email address is read-only.
3.  The user can edit their `name` field, and the change is successfully saved.
4.  The user has an option to upload or change their profile image.
5.  If an `image` URL exists for the user, it is displayed as their profile picture.
6.  The profile page contains a "Log Out" button that, when clicked, successfully ends the user's session and redirects them to the login page.

#### Story 2.2: 'Apply to be a Driver' Flow
**Goal**: To provide a clear and simple path for a rider to apply to become a driver.
**Acceptance Criteria**:
1.  A button or link labeled "Become a Driver" is displayed on the user's profile page.
2.  The button is visible to any authenticated user who does not yet have an associated `DriverProfile` record.
3.  When the user taps the button, the application opens the URL `https://excursionrides.com/drivers` in a secure, in-app browser.
4.  The transition to the in-app browser is handled smoothly, providing a seamless user experience.

#### Story 2.3: Admin Driver Approval Process
**Goal**: To implement the backend process allowing an administrator to manually approve a driver application.
**Note**: This story does not include the creation of an admin-facing UI. The approval is a manual backend action.
**Acceptance Criteria**:
1.  Following the external application process, a `DriverProfile` record is created for the user with the `isApproved` field set to `false`.
2.  A secure method (e.g., a database script or a secure internal tool) is established for a designated administrator to update a specific `DriverProfile`'s `isApproved` field from `false` to `true`.
3.  The update action is logged for auditing purposes, recording which user was approved and the timestamp of the approval.
4.  Once a user's `DriverProfile` is marked as approved, they will be granted access to driver-specific features upon their next session refresh.

#### Story 2.4: Approved Driver Vehicle Setup
**Goal**: To enable a newly approved driver to complete their profile by adding their required vehicle information.
**Acceptance Criteria**:
1.  A "My Vehicle" or "Driver Setup" section is accessible only to users whose `DriverProfile.isApproved` flag is set to `true`.
2.  This section contains a form with input fields for Vehicle Make, Model, Color, and License Plate.
3.  All form fields are required, and the input is validated to ensure it is not empty.
4.  Upon successful submission, a new `Vehicle` record is created in the database and correctly associated with the driver's profile.
5.  If vehicle information has already been provided, the page displays the currently saved information.
6.  The driver can edit and update their existing vehicle information.

#### Story 2.5: Driver Payout Onboarding Prompt
**Goal**: To prompt the approved driver to begin the Stripe Connect onboarding process (the actual integration will be in Epic 4).
**Note**: This story covers the UI prompt and the initiation of the flow. The backend logic to generate the Stripe Connect link will be implemented in Epic 4.
**Acceptance Criteria**:
1.  After a driver is approved (`isApproved = true`) and has added their vehicle, a prominent prompt is displayed on their main dashboard, such as "Set Up Payouts to Start Driving".
2.  The prompt is only visible if the driver has not yet completed the Stripe Connect onboarding.
3.  Tapping the prompt initiates a call to a backend endpoint to request a Stripe Connect onboarding link.
4.  Upon successfully receiving the onboarding link, the application securely redirects the user to the Stripe-hosted onboarding page.
5.  Once the driver's payout account is successfully connected (which will be handled in Epic 4), this prompt is permanently hidden from their dashboard.

### Epic 3: Ride Booking System

**Goal:** To implement the end-to-end ride booking experience, including fare calculation, trip requests, and driver matching logic.

#### Story 3.1: Implement FareTier Model & Seeding
**Goal**: To create the database model for fare tiers and populate it with initial pricing data.
**Acceptance Criteria**:
1.  A database migration script is created that defines the `FareTier` table in the Supabase database.
2.  The `FareTier` table schema includes all required fields: `displayName`, `priceCents`, `serviceType`, `vehicleType`, `tripType`, `maxDistance`, and `isActive`.
3.  The migration script runs successfully against the database, creating the new table correctly.
4.  A seed script is created to populate the `FareTier` table with the initial pricing data provided by the business owners.
5.  After the seed script is run, the `FareTier` table contains the correct initial data, which can be queried and verified.

#### Story 3.2: Ride Booking UI & Location Input
**Goal**: To build the user interface for riders to input their pickup and destination addresses using Google Places Autocomplete.
**Acceptance Criteria**:
1.  The main rider home screen displays a map view with two clear input fields: "Pickup location" and "Destination".
2.  As a user types an address into either input field, the UI calls the Google Places Autocomplete API to fetch and display relevant address suggestions in a dropdown list.
3.  When the user selects an address from the suggestions list, the corresponding input field is populated with the selected address text.
4.  Upon address selection, the geographic coordinates (latitude and longitude) are retrieved and stored in the application's client-side state, ready for distance calculation.
5.  The user interface is responsive and provides a touch-friendly experience for both typing and selecting addresses on mobile devices.

#### Story 3.3: Fare Calculation & Display Logic
**Goal**: To implement the backend logic that calculates trip distance, finds all valid fare tiers from the database, and displays them to the rider.
**Acceptance Criteria**:
1.  After a user provides both pickup and destination coordinates, a secure backend API endpoint is called to calculate available fares.
2.  The backend endpoint uses the Google Maps Directions API to determine the total travel distance for the requested route.
3.  The backend logic queries the `FareTier` database table to find all `isActive` tiers where the calculated distance is less than or equal to the tier's `maxDistance`.
4.  The API returns a list of all valid fare tiers to the client, sorted by `priceCents` in ascending order.
5.  The frontend UI receives the list and displays the fare options clearly to the rider (e.g., "Standard Car - $25.00").
6.  The cheapest valid fare option returned from the API is pre-selected in the UI by default.
7.  If no valid fare tiers are returned, the UI displays a clear message to the user, such as "We're sorry, we don't offer rides for this distance."

#### Story 3.4: Ride Confirmation & Initial Matching
**Goal**: To allow a rider to confirm their selected fare, authorize the payment with Stripe, and trigger the initial driver matching process.
**Acceptance Criteria**:
1.  A "Request Ride" button on the fare selection screen is enabled once a rider has selected a fare.
2.  When the user taps "Request Ride," a backend endpoint is called to create the trip.
3.  The backend initiates a payment authorization hold on the user's default payment method via the Stripe API for the selected fare amount.
4.  If the payment authorization succeeds, a new `Trip` record is created in the database with a status of `searching`, and the Stripe Payment Intent ID is stored with the record.
5.  If the payment authorization fails, no `Trip` record is created, and a clear error message is displayed to the rider in the UI (e.g., "Payment failed. Please check your card details.").
6.  Upon successful `Trip` creation, the backend's driver matching process is immediately triggered to find nearby, available drivers.
7.  The rider's UI updates to show a "Searching for Driver..." status, providing clear feedback that the request is being processed.

#### Story 3.5: Driver Ride Request Interface
**Goal**: To create the UI for an available driver to receive, view the details of, and respond to a new ride request.
**Acceptance Criteria**:
1.  When a ride request is broadcast to an available driver, they receive a push notification.
2.  The incoming request appears on the driver's main screen as a prominent UI element (e.g., a modal dialog).
3.  The request UI displays the essential details: the rider's pickup location, the estimated travel time to that pickup point, and the fixed fare the driver will earn.
4.  The UI provides the driver with clear "Accept" and "Decline" buttons.
5.  A countdown timer is displayed, showing the driver how much time they have to respond before the request automatically expires.
6.  If the driver does not respond within the allotted time, the request UI disappears, and the system considers it declined.

#### Story 3.6: Trip Acceptance & State Management
**Goal**: To handle the system state changes when a driver accepts a trip, notifying both the rider and driver of the successful match.
**Acceptance Criteria**:
1.  When a driver taps the "Accept" button, a backend endpoint is called to update the `Trip` record.
2.  The `Trip` record in the database is updated with the accepting driver's ID, and its status is changed to `en_route`.
3.  Any other drivers who were notified about this request are informed that the ride has been taken, and the request is removed from their interface.
4.  The accepting driver's UI transitions to an "active trip" screen, which displays the rider's pickup address and a button to copy it.
5.  Simultaneously, the rider's UI updates from "Searching for Driver..." to show that a driver is on the way.
6.  The rider's "active trip" screen displays the driver's name, vehicle information (make, model, color, license plate), and their average star rating.
7.  The rider's screen also prominently displays the unique 4-digit PIN required for pickup verification.

### Epic 4: Payment Integration

**Goal:** To integrate Stripe for handling all payment-related features, including adding payment methods, processing ride fares, managing tips, and enabling driver payouts.

#### Story 4.1: Rider Payment Method Management
**Goal**: To allow riders to securely add, view, and remove credit card payment methods using Stripe Elements.
**Acceptance Criteria**:
1.  An authenticated user can navigate to a "Wallet" or "Payment Methods" section within their profile.
2.  This section displays a list of the user's saved payment methods, showing the card brand and a masked number (e.g., "Visa ending in 4242").
3.  The user can select one of their saved cards and set it as their default payment method.
4.  A button to "Add Payment Method" is present, which opens a secure form using Stripe Elements.
5.  A user can successfully add a new card, which is securely saved to their customer profile in Stripe, not in our application database.
6.  The backend logic creates a Stripe Customer object for the user if one does not already exist.
7.  The user can remove a saved payment method, which detaches it from their Stripe Customer profile.

#### Story 4.2: Driver Payout Account Onboarding
**Goal**: To implement the full Stripe Connect onboarding flow, allowing approved drivers to securely provide their banking information for payouts.
**Acceptance Criteria**:
1.  When a driver clicks the "Set Up Payouts" prompt (from Story 2.5), a secure backend endpoint is called to generate a Stripe Connect onboarding link.
2.  The backend logic creates a Stripe Express account for the driver if one does not already exist.
3.  A unique, one-time account link is generated using the Stripe API, which will redirect the user to a secure, Stripe-hosted onboarding page.
4.  The user is successfully redirected to the Stripe-hosted page where they can securely enter their identity and banking details.
5.  A backend webhook endpoint is implemented to listen for `account.updated` events from Stripe to track the status of the onboarding process.
6.  Upon successful completion of the onboarding flow, the webhook updates the driver's `DriverProfile` in our database with their `stripeAccountId` and marks their payout account as enabled.
7.  After completing the process on Stripe, the user is redirected back to a confirmation page within our application.

#### Story 4.3: Automatic Fare Payment Capture
**Goal**: To implement the backend logic that automatically captures the pre-authorized payment from the rider's card upon successful trip completion.
**Acceptance Criteria**:
1.  When a `Trip`'s status is updated to `complete`, a backend process is automatically triggered to handle the payment capture.
2.  The backend retrieves the `stripePaymentIntentId` that was created and stored with the `Trip` record during the initial authorization (in Story 3.4).
3.  The system sends a `capture` command to the Stripe API for the correct fare amount using the stored Payment Intent ID.
4.  Upon successful capture, the final Stripe transaction ID is saved and associated with the `Trip` record in the database for reconciliation.
5.  If the payment capture fails (e.g., due to an expired hold or insufficient funds), the system logs the error and flags the trip for manual administrative review.
6.  The system does not automatically retry a failed capture to prevent multiple charges.

#### Story 4.4: Post-Ride Tipping Flow
**Goal**: To create the UI and backend logic for riders to add an optional tip for the driver after a ride is completed.
**Acceptance Criteria**:
1.  After a rider submits their rating for a completed trip, the UI immediately transitions to a tipping screen.
2.  The tipping screen displays several pre-set tip options (e.g., 15%, 20%, 25% of the fare) and an input field for a custom tip amount.
3.  The UI provides a "No Tip" or "Skip" option for the user to close the tipping screen without adding a tip.
4.  When a user confirms a tip amount, a **new, separate charge** is initiated against their default payment method via the Stripe API.
5.  A record of the successful tip transaction is saved and associated with the `Trip` record for the driver's earnings calculation.
6.  After the tipping action is completed (either by adding a tip or skipping), the user is navigated back to the main application screen.

#### Story 4.5: Scheduled Driver Payouts
**Goal**: To create the automated backend process that calculates and transfers driver earnings to their connected Stripe accounts on a regular schedule.
**Acceptance Criteria**:
1.  A scheduled backend process (e.g., a Vercel Cron Job) is configured to run on a regular, defined schedule.
2.  When triggered, the process identifies all drivers with pending earnings and a valid, fully onboarded Stripe Connect account.
3.  For each eligible driver, the process accurately calculates their net earnings, defined as total fares plus total tips minus the platform fee, since their last payout.
4.  The system successfully initiates a payout to each eligible driver's connected bank account via the Stripe Connect API.
5.  A detailed record of each payout transaction is created and stored in the database for bookkeeping.
6.  The driver's "Earnings" screen in the application is updated to display their payout history.

### Epic 5: Real-time Features & Notifications

**Goal:** To leverage Supabase Realtime and Firebase Cloud Messaging to enable live ride tracking, in-app chat, and push notifications.

#### Story 5.1: Real-time In-App Chat
**Goal**: To implement a real-time chat interface using Supabase Realtime for riders and drivers to communicate during an active trip.
**Acceptance Criteria**:
1.  A chat interface is available to both the rider and driver on their respective "active trip" screens, accessible only after a driver has accepted the trip.
2.  The backend uses Supabase Realtime subscriptions to broadcast new messages to the specific trip's channel.
3.  When a user sends a message, it is saved to the `Message` table in the database and appears in near real-time on the other user's chat interface.
4.  The driver's chat UI includes a list of tappable, pre-populated "quick reply" messages, one of which dynamically includes their current ETA (e.g., "I'm on my way, my ETA is ~7 minutes.").
5.  The rider's chat UI includes simple "quick reply" options (e.g., "OK," "Thank you").
6.  The chat functionality is disabled (e.g., input hidden or disabled) once the trip status is `complete` or `canceled`.

#### Story 5.2: "Notify Me" Push Notifications
**Goal**: To implement the push notification system using Firebase Cloud Messaging for the 'virtual queue' feature when no drivers are initially available.
**Acceptance Criteria**:
1.  When a rider's search for a driver fails, they are presented with a "Notify Me When Available" option.
2.  When a rider opts in, their trip request is saved to a virtual queue with a timestamp.
3.  A backend process identifies the longest-waiting user in the queue when a driver becomes available in their area.
4.  The system creates a temporary, 90-second exclusive `RideOffer` for the selected rider and driver.
5.  A push notification is successfully sent to the rider's device via Firebase Cloud Messaging (FCM), alerting them of the exclusive ride offer.
6.  Tapping the push notification deep-links the user into the application, directly to an offer screen.
7.  The offer screen displays the ride details and a 90-second countdown timer, prompting the user to confirm their ride.

#### Story 5.3: Live Driver Location Tracking
**Goal**: To provide riders with a real-time view of their driver's location on a map after a trip is accepted.
**Acceptance Criteria**:
1.  After a driver accepts a trip, their device begins broadcasting its location coordinates to a dedicated Supabase Realtime channel for that trip.
2.  The rider's "active trip" screen subscribes to this Supabase Realtime channel to receive location updates.
3.  A driver icon on the rider's map view updates its position in near real-time as new coordinates are received.
4.  The location broadcasting is only active while the trip status is `en_route` or `picked_up` to preserve privacy and battery.
5.  The broadcasting automatically stops once the trip status changes to `complete` or `canceled`.
6.  The implementation is optimized to minimize battery consumption on the driver's device.

#### Story 5.4: Trip Status Push Notifications
**Goal**: To send push notifications for other critical trip updates, such as 'Your driver has arrived'.
**Acceptance Criteria**:
1.  A push notification is sent to the rider via Firebase Cloud Messaging (FCM) when their driver is approximately 1-2 minutes away from the pickup location.
2.  A push notification is sent to the rider when the driver marks themselves as "arrived" at the pickup location.
3.  A push notification is sent to the rider if their trip is canceled by the driver.
4.  All push notifications, when tapped, deep-link the user to the relevant screen within the application (e.g., the active trip screen).

---

## 7. Checklist Results Report
*(This section will be populated after the PO Master Validation)*

## 8. Next Steps
*(This section will be populated after the planning phase is complete)*