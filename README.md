# DrainWatch AI — local operations app

This repository contains the DrainWatch AI project: the browser based operations dashboard, simulation, backend and web portals, ML scripts, and Android apps. The static dashboard at the repository root is the easiest part to run first.

## Project layout

- `index.html`, `features.js`, and `features.css`: static browser dashboard.
- `DrainWatch Simulation/`: standalone flood and drain simulation.
- `DrainWatch Rescue Laptop/drainwatch/`: backend, public/admin/station portals, and ML source.
- `mobile/`: Android public and rescue apps with shared components.
- `DrainWatch Rescue Laptop/`: local launch helpers and project notes. The bundled Node runtime, logs, and PID files are intentionally excluded.

## Run the static dashboard

From this folder, run `python -m http.server 8000` and open `http://localhost:8000/`.

## Backend setup

The backend source is in `DrainWatch Rescue Laptop/drainwatch/backend/`. Install dependencies with `npm ci`, copy `.env.example` to `.env`, then set local values in `.env` before starting the server with `npm start`. Never commit `.env` or real credentials.

The Android project is in `mobile/` and uses the included Gradle wrapper. See `mobile/README.md` for project-specific notes.

## Run

The live local app is served at `http://localhost:8000/`. From this `outputs` folder, start it with `python -m http.server 8000` if it is not already running. No package installation or backend is required. Network access is needed for map/weather providers, the optional point forecast, and public route alternatives.

## Features

- **Live map and weather:** existing interactive map, forecast tiles, selected-point model data, rainfall screening, weather layers, and playback remain present. Providers are attributed in the UI. Forecast output is model data, not observed radar or a validated flood prediction.
- **Operations dashboard:** selected-point summary, local incident count, source/data distinction, and browser capability status.
- **Prediction scenarios:** user-entered scenarios use the same rain-only rule as the dashboard (`min(100, rainfall mm × 7)`), optionally adding 25 for a user-reported blocked drain. Results are unvalidated screening, not flood probability.
- **Point forecast by coordinates/GPS:** users can enter coordinates or choose the GPS button, then request a six-hour Open-Meteo point forecast. The request sends the chosen coordinates to Open-Meteo. Coordinates/results stay in the current page session unless attached to a report. No separate GPS permission was granted during verification.
- **Route alternatives:** OSRM public demo alternatives with estimated route distance/time; reports with GPS coordinates within approximately 300 m are shown as unverified local hazards. Manual comparison remains available. It does not check traffic, closures, water depth, road access, or guarantee safety.
- **Alerts:** local rainfall threshold rule. Optional browser notifications are foreground-only while the page is open; no push server, background service, SMS, or delivery guarantee.
- **Historical data:** manually add local events or import CSV (`area,date,type,source`; optional `latitude,longitude`). Records remain in this browser and are not an official citywide dataset.
- **Model comparison:** illustrative synthetic rule table plus an optional local CSV evaluation (`timestamp,rainfall6h,blocked,waterlogged`). It uses the first 80% of chronological records for logistic-model training and the last 20% as holdout, comparing it with the dashboard rain-only rule. It requires at least 20 valid records and both outcome classes in train and test. Metrics apply only to the supplied data; they do not establish production accuracy.
- **Community reports:** local report with optional photo, optional GPS attachment, persistence, JSON download, and local incident creation.
- **Complaint workflow:** generate, download, or share a complaint draft. The app does not submit it or claim an acknowledgement.
- **Public SOS and responder queue:** local intake, queue, assignment/status changes, and audit trail. No dispatcher or responder receives records.
- **Radio/news leads:** manual unverified leads and a user-initiated RSS/Atom fetch attempt. Browser cross-origin policy may block feeds. Speech-to-text, verification, and official alert publishing are not connected.
- **Settings/privacy:** local records export/clear controls, browser permission status, and notices for external coordinate use.
- **Responsive navigation:** the sidebar has no “NEXT” badges. Every feature page is reachable; small-screen styles are included.

## External service boundaries and blockers

- **Forecast:** [Open-Meteo forecast API](https://open-meteo.com/en/docs) returned the separate point forecast during the Chrome check. Data and model availability can vary.
- **Routing:** [OSRM routing API](https://project-osrm.org/docs/v5.24.0/api/) returned a route for the sample Bangalore coordinates. The public demo endpoint has no availability commitment and is not a flood-aware routing service.
- **RSS:** a public BBC RSS URL was attempted in Chrome, but browser fetch failed (likely CORS or provider access). The app displays the failure and keeps manual lead entry available.
- **CSV upload:** import UI is present, but the connected Chrome extension currently blocks selecting local files until “Allow access to file URLs” is enabled for the extension. That security setting was not changed, so a full browser upload test could not be completed here.
- **GPS and notifications:** user-initiated controls are implemented; no browser location or notification permission was granted during verification. Manual coordinate forecast was verified.
- **Historical hotspots and validated prediction:** require verified geocoded incident data plus independent labels and a suitable evaluation plan. No official dataset or model accuracy is claimed.
- **Municipal submission, responder dispatch, and public SOS delivery:** need the target jurisdiction, official authorized intake/service, backend, and operational owner. The current flows stay local and explicitly show that they are not submitted.
- **Feed automation/transcription:** needs a reachable lawful feed, source terms, and an authorized speech-to-text service for audio.

## Verification performed in Chrome

- Confirmed every sidebar feature page opens; no fallback “coming later” overlay remained, and no sidebar “NEXT” badge is visible.
- Checked scenario rule: 10 mm produced 70/100; checked model table uses the same formula.
- Checked a separate point forecast request for manually entered coordinates; provider data rendered, and no coordinates were persisted by that feature.
- Checked OSRM sample alternatives; one 1.5 km route was returned, with the local-report/no-report warning visible.
- Checked a local-only labeled-dataset evaluation path with a temporary synthetic fixture. It produced chronological holdout metrics, and the page labels them as dataset-only, not independent validation. The fixture was not stored.
- Attempted a public RSS fetch; Chrome blocked it and the UI showed a truthful failure.
- Checked settings, history import UI, and feature navigation. A file upload could not be verified because of the Chrome extension file-URL permission blocker above.
- Existing browser local storage contains demonstration/test records from feature checks, including a fictional report and responder incidents. Review Settings → Export or Clear demo records; clearing asks for confirmation.
- JavaScript syntax check passed. Browser console also showed the expected failed RSS fetch from the extension’s cross-origin fetch wrapper; no syntax error remained after correction.

## Important limits

This is still a static browser app with local storage. It is not a hosted emergency service, municipal system, validated AI flood model, or guaranteed safe-route service. The exact DrainWatch literature review, IEEE-links PDF, and project plan were not available in the accessible workspace, so paper-specific compliance is not claimed.
