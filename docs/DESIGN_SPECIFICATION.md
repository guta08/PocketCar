# PocketCar — Visual Design Specification

Status: Approved visual direction / concept baseline
Date: 2026-11-12
Product: PocketCar
Target: iOS + Android
Launch language: English; Romanian localization supported
Themes: Light and Dark, follow system by default
Design style: Minimalist, balanced information density
Navigation: Bottom navigation

## 1. Approved direction

The approved direction is a clean, minimalist automotive companion app. The interface must feel trustworthy, modern, calm and premium without heavy glassmorphism, excessive gradients, dense dashboards or decorative effects.

The main dashboard is vehicle-first: a prominent vehicle visual, identity details and clear status cards. The design must support multiple vehicles and adapt to phones of different sizes.

## 2. Proposed color system

Use semantic tokens rather than hard-coded colors in components. Initial proposal:

| Token | Light | Dark |
|---|---|---|
| background | #F5F7FB | #0F1B2D |
| surface | #FFFFFF | #1B2A3E |
| surfaceElevated | #FFFFFF | #22344B |
| textPrimary | #152238 | #F2F6FC |
| textSecondary | #718096 | #9AAAC0 |
| border | #E1E8F0 | #2A3C54 |
| primary | #2563EB | #5B8DEF |
| success | #16A34A | #22C55E |
| warning | #D97706 | #F59E0B |
| critical | #DC2626 | #F87171 |
| info | #0EA5E9 | #38BDF8 |

The palette is a proposal represented in the concept board. Verify WCAG 2.2 AA contrast for all text and interactive states before implementation. Never rely on color alone to convey expiry or severity; pair color with text/icon.

## 3. Typography and layout

- Use a highly legible system sans-serif (SF Pro on iOS / Roboto on Android) or a consistent cross-platform family if the chosen stack supports it.
- Use a clear type scale: screen title, section heading, card title, body, metadata/caption.
- Use an 8 pt spacing grid; base screen horizontal inset 16–20 dp.
- Card radius: 14–20 dp; controls and chips 10–14 dp.
- Touch targets minimum 44×44 pt (iOS) / 48×48 dp (Android).
- Respect safe areas, dynamic type/font scaling, keyboard insets and device accessibility settings.
- Use responsive constraints, not fixed screenshot dimensions.

## 4. Dashboard composition

Priority order:
1. Top app bar: PocketCar wordmark, profile/settings or notification entry point.
2. Greeting/summary, concise and optional if space is constrained.
3. Featured vehicle card: large vehicle photograph or rendered image; vehicle name/model/year; masked VIN; registration number; vehicle switcher when multiple vehicles exist.
4. Status overview: document validity and next maintenance. Use clear urgency labels and dates/distances.
5. Quick access: secure documents and service history.
6. Bottom navigation.

The five requested dashboard groups are:
- Vehicle and technical information
- RCA/CASCO/ITP/rovinieta validity (display localized names where appropriate; internal domain model remains country-aware)
- Next maintenance
- Notifications/alerts
- Quick access to documents

Avoid placing every detail on the dashboard. Use concise cards with navigation to detail pages.

## 5. Navigation

Use a persistent bottom navigation bar. Initial recommended destinations:
- Home
- Garage
- Service
- Documents
- Profile

Notifications may be an app-bar action or an item in Home/Profile depending on final information architecture. Keep labels visible where practical; use accessible icons with labels and selected states.

## 6. Vehicle imagery

Use a hybrid approach:
- Realistic photography for vehicle hero imagery where a real vehicle photo is supplied or licensed.
- High-quality 3D renders/illustrations for default vehicle placeholders, onboarding, empty states and generic vehicle categories.
- Do not use manufacturer logos, trademarks or copyrighted vehicle model assets without permission.
- Vehicle images must not imply exact trim/color/condition based on VIN unless verified.
- Provide fallback artwork when no photo exists.
- Optimize images for mobile bandwidth and cache appropriately.

## 7. Required screen inventory

Implement and design these screens/states:
1. Onboarding / account sign-in and permission education.
2. Garage list with multiple vehicles, add vehicle CTA and empty state.
3. Add vehicle by VIN, loading, VIN invalid, decoded details confirmation and manual correction.
4. Home dashboard with one and multiple vehicles.
5. Vehicle details / technical specifications.
6. Validity overview and detail for RCA, CASCO, ITP, rovinieta; unknown/unverified data state.
7. Service overview, maintenance schedule, service item detail, add/edit maintenance, history.
8. Private document vault: locked, unlocked, empty, list, category, document detail, import/scan.
9. Document capture and crop/deskew/enhancement review. User must approve the processed scan before saving.
10. Notifications center and notification preferences.
11. Profile/settings, theme (system/light/dark), language, account, backup/security.
12. Loading, offline, permission denied, network error, empty, success, destructive confirmation and retry states.

## 8. Privacy and security visual requirements

- Document vault must have a distinct locked state and a clear unlock flow.
- Support biometrics (Face ID / Touch ID / Android BiometricPrompt) when available, with device passcode/PIN fallback according to platform capabilities.
- Never visually promise biometrics on devices that do not support them.
- Hide sensitive document previews in app switcher/task snapshots where platform APIs permit.
- Mask VIN and sensitive identifiers in overview surfaces; reveal only on deliberate action.
- Document scan processing should show capture → auto crop/perspective correction → user review → save. Do not silently store an unreviewed capture.
- Do not display sensitive document contents in push notifications or email previews.

## 9. Interaction and accessibility

- Every card that looks interactive must be actionable.
- Provide pressed, focused, disabled, loading, selected and error states.
- Provide text labels for status, not only color.
- Support screen readers and semantic labels for all icon-only controls.
- Respect reduced motion; animations are short and purposeful.
- Confirm destructive actions and preserve unsaved form data when possible.
- Use clear date, mileage and unit formatting, localized per language/region.

## 10. Qoder implementation rules

1. Treat this document as the approved visual direction, not as permission to copy the SVG mockup pixel-for-pixel.
2. Treat `assets/design/pocketcar-concept-board.svg` as a visual reference for hierarchy, spacing, color direction and Light/Dark parity.
3. Recreate the UI with native, reusable components in the project's selected framework. Do not embed the SVG as the app UI.
4. Build a token layer for colors, typography, spacing, radii, elevation and component states.
5. Implement both themes and system-following behavior from the start.
6. Use responsive layouts and platform safe areas; do not hard-code the board's 1440×1000 dimensions.
7. Keep content and labels localization-ready; English is the launch locale and Romanian must be supported.
8. Use realistic licensed/supplied images or original renders; do not scrape brand imagery.
9. Implement loading/empty/error/offline/permission/locked/unlocked states, not only the happy path.
10. Before calling a screen complete, test small and large phone sizes, Light/Dark, accessibility text scaling and keyboard behavior.

## 11. Acceptance criteria for the design implementation

- Dashboard hierarchy clearly prioritizes the selected vehicle.
- User can switch between multiple vehicles without losing context.
- Light and Dark themes are both complete and follow system setting by default.
- Every dashboard module navigates to a meaningful destination.
- Bottom navigation remains consistent and accessible.
- Text contrast meets WCAG 2.2 AA.
- Sensitive document content is not exposed in locked states or notifications.
- UI is localization-ready for English and Romanian.
- No layout clipping/overlap at supported font scales and common phone sizes.

## 12. Scope boundary

This is a visual baseline and implementation brief. The concept board is not a final production design for every screen. Qoder must implement the full screen inventory and states above, then report screenshots for review before considering the UI phase complete.
