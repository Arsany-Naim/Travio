# Travio (TravelHub)

Travio is a Flutter travel booking and planning app powered by Supabase. Users can browse trips, book and save favorites, explore destinations on a map, and join community groups. The app also includes an admin dashboard and optional AI-assisted features for itineraries, meeting points, and location data.

## Features
- Browse trips, search by destination and budget, and view trip details
- Book trips with guests, travel date, seat category, meeting point, and special requests
- Save favorites and manage bookings
- Community groups (public and private) with group creation and members
- Admin dashboard for managing users and trips
- Map view with trip markers and an itinerary map view
- AI-assisted coordinates, itineraries, and meeting points (Gemini) with Supabase caching
- Fallback offline trip data when Supabase is unavailable

## Tech stack
- Flutter and Dart
- Supabase (Auth + Postgres)
- Provider state management
- flutter_map + OpenStreetMap tiles
- HTTP client and dotenv

## Getting started

### Prerequisites
- Flutter SDK (Dart 3.9+)
- A Supabase project

### Install dependencies
```
flutter pub get
```

### Configure Supabase
1. Create a Supabase project.
2. In the Supabase SQL editor, run:
	 - `supabase_admin_setup.sql` (trips + profiles + seed data)
	 - `supabase_setup.sql` (favorites + bookings)
3. Ensure the bookings table has a `meeting_point` column (used during booking):
```
ALTER TABLE bookings ADD COLUMN IF NOT EXISTS meeting_point TEXT;
```

### App credentials
Supabase credentials are currently hardcoded in `lib/main.dart`. Replace them with your own:
- `Supabase.initialize(url: ..., anonKey: ...)`

The app also loads `.env` at startup, so you can refactor to read these values from environment variables if you prefer.

### Optional AI features (Gemini)
AI features are implemented in `lib/services/ai_location_service.dart` and are used for:
- Trip coordinates (map markers)
- Trip itineraries (day-by-day map view)
- Meeting points
- Location suggestions

Set your Gemini API key in `_geminiApiKey` or refactor to read it from `.env`.

If you want persistence for AI data, create these tables:
```
-- Trip coordinates cache
CREATE TABLE IF NOT EXISTS trip_coordinates (
	trip_id UUID REFERENCES trips(id) ON DELETE CASCADE PRIMARY KEY,
	location TEXT,
	latitude DOUBLE PRECISION,
	longitude DOUBLE PRECISION
);

-- Itinerary cache
CREATE TABLE IF NOT EXISTS trip_itineraries (
	id BIGSERIAL PRIMARY KEY,
	trip_id TEXT NOT NULL,
	day INTEGER NOT NULL,
	title TEXT,
	description TEXT,
	latitude DOUBLE PRECISION,
	longitude DOUBLE PRECISION,
	activities TEXT,
	time TEXT
);

-- Meeting points cache
CREATE TABLE IF NOT EXISTS meeting_points (
	id BIGSERIAL PRIMARY KEY,
	trip_id UUID REFERENCES trips(id) ON DELETE CASCADE,
	name TEXT,
	description TEXT,
	latitude DOUBLE PRECISION,
	longitude DOUBLE PRECISION,
	icon_type TEXT
);

-- Location suggestions cache
CREATE TABLE IF NOT EXISTS location_suggestions (
	id BIGSERIAL PRIMARY KEY,
	query TEXT UNIQUE,
	suggestions TEXT,
	expires_at TIMESTAMP DEFAULT (NOW() + INTERVAL '7 days')
);
```

### Run the app
```
flutter run
```

For web:
```
flutter run -d chrome
```

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
	services/        Supabase and AI services
	widgets/         Reusable UI components
```

## Notes
- Trip data falls back to local data if Supabase is unavailable.
- Map and itinerary features can work with cached or fallback coordinates.
