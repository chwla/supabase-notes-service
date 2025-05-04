Supabase Notes Mini-Project
Project Overview
This is a minimal backend for a personal notes app built using Supabase. It includes:

A single notes table schema

Two Supabase Edge Functions (post_notes and get_notes)

Authenticated access for each user’s notes

Setup & Deployment Steps
Clone the repo and initialize Supabase:

bash
supabase init
Apply the schema:

bash
supabase db reset
Set environment variables in your Supabase project (via dashboard or CLI):

ini

SUPABASE_URL=your-supabase-url
SUPABASE_ANON_KEY=your-anon-key
Deploy edge functions:

bash
supabase functions deploy post_notes
supabase functions deploy get_notes
Schema Design
File: schema.sql

sql
create table if not exists notes (
  id uuid primary key default gen_random_uuid(),
  user_id uuid references auth.users(id),
  title text not null,
  content text,
  created_at timestamp with time zone default current_timestamp
);
Design Choices
UUID used as the primary key for scalability and uniqueness.

user_id references auth.users to associate notes with authenticated users.

title is required. content is optional for flexibility.

created_at defaults to current time for automatic timestamping.

Edge Functions
post_notes.ts
Handles POST /notes to create a new note.

ts
// Why: POST method is standard for creating resources; body used for passing title/content securely.
get_notes.ts
Handles GET /notes to fetch all notes for the authenticated user.

ts
// Why: GET method is standard for retrieving data; no body needed, only auth token.
Demo Commands
Create a Note
bash
Copy
curl -X POST https://your-project-id.functions.supabase.co/post_notes \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"title": "Test Note", "content": "This is a test"}'
Expected response:

json

[
  {
    "id": "uuid-here",
    "user_id": "user-uuid",
    "title": "Test Note",
    "content": "This is a test",
    "created_at": "timestamp"
  }
]
List All Notes
bash

curl -X GET https://your-project-id.functions.supabase.co/get_notes \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
Expected response:

json

[
  {
    "id": "uuid-here",
    "user_id": "user-uuid",
    "title": "Test Note",
    "content": "This is a test",
    "created_at": "timestamp"
  }
]
