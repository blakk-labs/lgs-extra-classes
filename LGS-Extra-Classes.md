This is a good fit for a PWA, because the students who most need it are likely on patchy, expensive data. So the plan below puts offline use and low data cost first.

## 1. Core design principles

- **Offline-first.** A student opens a note once on Wi-Fi or a good signal, and it stays readable with no internet afterwards.
- **Light on data.** Text-heavy notes, compressed images, and no autoplay video.
- **Cheap-phone friendly.** Many students will use low-end Android phones, so keep the bundle small and avoid heavy animations.
- **Simple for teachers.** If note creation is painful, nobody will use it.

## 2. Users and roles

| Role | Main needs |
|---|---|
| **Student** | Browse by class and subject, read notes, save for offline, track progress |
| **Teacher** | Create, edit, and publish notes for their subjects and classes |
| **Admin** (school head or IT person) | Manage teacher and student accounts, classes, subjects |
| **Parent** (optional, later) | See what their child has been studying |

## 3. Features, in phases

**Phase 1: MVP**
- Login for students and teachers (class and student ID, or phone number with a PIN, since many JHS students have no email)
- Content organized as Class (JHS 1–3), then Subject, then Topic, then Note
- Teacher note editor with rich text, images, and tables
- Student note reader that is clean, mobile-first, and has adjustable font size
- Offline reading of notes the student has opened or saved
- Installable on the home screen

**Phase 2: Learning support**
- Short quizzes at the end of a note (multiple choice, instant feedback)
- Bookmarks and "mark as completed"
- Search across notes
- Past questions (BECE-style) by subject
- Announcements from teachers

**Phase 3: Engagement**
- Question-and-comment thread per note, so students can ask teachers
- Push notifications for new notes
- Simple progress reports per student
- Audio or short video lessons, optional and downloadable on Wi-Fi

## 4. Suggested tech stack

- **Frontend:** React with Vite, React Router, and Tailwind CSS
- **PWA:** `vite-plugin-pwa`, which uses Workbox for the service worker and manifest
- **Offline storage:** IndexedDB through Dexie.js, for saved notes and queued actions
- **State and data fetching:** TanStack Query, which has good caching and offline support
- **Rich text editor (teachers):** TipTap, which is React-friendly and outputs clean HTML or JSON
- **Backend:** Supabase is the quickest route (Postgres, auth, storage, row-level security). Firebase also works. If you prefer your own backend, Node with Express and PostgreSQL is a solid choice.
- **Hosting:** Vercel or Netlify for the frontend, and Supabase for the backend

## 5. Data model (simplified)

- `users` (id, name, role, class_id, phone or student_id)
- `classes` (id, name: JHS 1, JHS 2, JHS 3)
- `subjects` (id, name)
- `teacher_assignments` (teacher_id, subject_id, class_id)
- `topics` (id, subject_id, class_id, title, order)
- `notes` (id, topic_id, author_id, title, content, status: draft/published, updated_at)
- `quizzes` and `quiz_questions` (linked to notes)
- `progress` (student_id, note_id, completed, last_read)
- `comments` (note_id, user_id, text, parent_id)

## 6. Offline and PWA strategy

- **App shell cached** by the service worker, so the app opens instantly even with no signal.
- **Notes cached on read**, with a "Download for offline" button for whole topics.
- **Sync on reconnect.** Progress and quiz results are saved locally first, then pushed to the server when the connection returns.
- **Update prompt** ("New version available") so students don't get stuck on an old version.
- **Compress images** on upload (teachers shouldn't have to think about it) and use lazy loading.

## 7. Suggested project structure

```
src/
  features/
    auth/
    notes/        (reader + teacher editor)
    quizzes/
    progress/
  components/
  lib/            (api client, db, offline sync)
  pages/
  routes/
```

## 8. Things worth deciding early

1. **Content format.** Will teachers type notes directly into the app, upload Word or PDF files, or both? Typed notes work much better offline and on phones.
2. **Who creates accounts.** Self-signup is risky for a school, so I'd suggest the admin or teachers create student accounts in bulk from a class list.
3. **Devices.** Do students mostly use their own phones, a parent's phone, or shared devices? This affects login and offline design.
4. **Curriculum alignment.** Structuring topics by the NaCCA curriculum for each subject will make the content easy to navigate and trusted by teachers.
5. **Moderation.** Decide whether teachers can publish straight away or whether the head or a department lead must approve notes.

## 9. Rough timeline (one developer)

- **Step 1–2:** Setup, auth, data model, roles
- **Step 3–4:** Teacher editor and note publishing
- **Step 5–6:** Student reader, navigation, search
- **Step 7–8:** Offline and PWA, testing on real low-end phones
- **Step 9:** Pilot with one class, collect feedback
- **Step 10 onwards:** Quizzes, notifications, improvements

