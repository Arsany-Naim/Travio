# Travio

Travio is a Flutter travel booking and planning app with a polished multi-screen experience for discovering trips, managing bookings, and exploring destinations. The backend is optional, but if you connect one you unlock auth, bookings, favorites, and admin data.

## What you can do
- Discover featured trips and explore all destinations
- Search by destination and budget
- Book trips with guests, travel date, seat category, meeting point, and special requests
- Save favorites and review bookings
- View trips on an interactive map with markers
- Generate itineraries and meeting points (optional AI)
- Join or create travel groups in the community section
- Use an admin dashboard to manage users and trips (dev)

## Screens
- Home and Explore
- Trip Details and Booking Summary
- My Trips (Bookings and Favorites)
- Map Overview and Itinerary Map View
- Meeting Point Selector
- Community Groups
- Profile
- Admin Dashboard (dev)

## Tech stack
- Flutter and Dart
- Provider state management
- flutter_map + OpenStreetMap tiles
- HTTP client and dotenv
- Optional backend: Supabase (Auth + Postgres)

## Getting started

### Prerequisites
- Flutter SDK (Dart 3.9+)

### Install dependencies
```
flutter pub get
```

### Run the app
```
flutter run
```

For web:
```
flutter run -d chrome
```

## Configuration
The app loads `.env` at startup. You can store API keys and endpoints there and wire them in code.

Current code uses hardcoded values in `lib/main.dart` for backend initialization. Update those values or refactor to read from `.env`.

## Backend setup (optional)
If you want authentication, bookings, favorites, and admin data, connect a backend.

Quick setup with Supabase:
1. Create a Supabase project.
2. Run the SQL helpers:
  - `supabase_admin_setup.sql` (trips, profiles, seed data)
  - `supabase_setup.sql` (favorites, bookings)
3. Add the meeting point column (only if you use meeting point selection):
```
ALTER TABLE bookings ADD COLUMN IF NOT EXISTS meeting_point TEXT;
```
4. Update your backend URL and anon key in `lib/main.dart` or load them from `.env`.

If no backend is configured, the app falls back to local trip data.

## AI features (optional)
AI features are implemented in `lib/services/ai_location_service.dart`:
- Trip coordinates for maps
- Day-by-day itineraries
- Meeting points
- Location suggestions

Set your Gemini API key in the service or load it from `.env`.
If you want AI persistence, create tables for: `trip_coordinates`, `trip_itineraries`, `meeting_points`, and `location_suggestions`.

## Admin login (dev)
The app includes a simple admin shortcut in the auth modal:
- Email: admin@travilo.app
- Password: admin

Change this in `lib/auth/auth_modal.dart` before production.

## Project structure
```
lib/
  auth/            Auth modal and flow
  config/          Colors, enums, fallback trip data
  models/          Data models
  providers/       State management
  screens/         App screens (home, trips, map, admin, community)
  services/        API and AI services
  widgets/         Reusable UI components
```
