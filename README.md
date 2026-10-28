# LDIS School Management System (PWA)

Full-stack Progressive Web App for school management, built with **Next.js 15 (App Router) + TypeScript**, **Supabase Auth** and **PostgreSQL with Row Level Security**.

- 6 roles: Admin, Principal, Teacher, Student, Parent, Accountant
- 18 planned modules; **Students, Teachers, Notices, Attendance** are fully working, the remaining 14 are placeholders
- Installable PWA (manifest + service worker)
- Deploy: GitHub + Vercel

## Setup

1. Create a project at [supabase.com](https://supabase.com).
2. In the SQL Editor, run `supabase/migrations/001_schema.sql`.
3. Copy `.env.example` to `.env.local` and fill in your Supabase URL and anon key (Project Settings → API).
4. Install and run:

   ```bash
   npm install
   npm run dev
   ```

5. Sign up in the app. Every new user starts as `student`. Promote your first admin in the SQL Editor:

   ```sql
   update public.profiles set role = 'admin' where email = 'you@example.com';
   ```

6. Add some classes so attendance and students can use them:

   ```sql
   insert into public.classes (name, section) values ('Class 5', 'A'), ('Class 6', 'A');
   ```

## Roles & access (enforced by RLS in the database)

| Module     | Admin | Principal | Teacher | Accountant | Student | Parent |
|------------|:-----:|:---------:|:-------:|:----------:|:-------:|:------:|
| Students   | CRUD  | CRUD      | read    | read       | self    | children |
| Teachers   | CRUD  | CRUD      | read    | read       | –       | –      |
| Notices    | CRUD  | CRUD      | post/read | read     | read (audience) | read (audience) |
| Attendance | mark  | mark      | mark    | –          | self    | children |

To link a parent to a child, insert into `parent_students (parent_id, student_id)`. To link a student login, set `students.profile_id`.

## Deploy to Vercel

1. Push this repo to GitHub.
2. Import it in Vercel.
3. Add `NEXT_PUBLIC_SUPABASE_URL` and `NEXT_PUBLIC_SUPABASE_ANON_KEY` as environment variables.
4. In Supabase → Authentication → URL Configuration, add your Vercel URL as the Site URL.

## Project structure

```
src/app/login            Sign in / sign up
src/app/(app)/*          Protected app (sidebar layout, role-aware)
src/lib/supabase/*       Browser/server clients + session middleware
src/lib/roles.ts         Roles and the 18-module registry
supabase/migrations      Schema + RLS policies
public/                  manifest.json, sw.js, icon.svg
```
