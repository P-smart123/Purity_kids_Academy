# Purity Kids Academy — Result Management Portal

A single-file, fully working school portal: `index.html` contains all HTML, CSS and
JavaScript. The backend is **Firebase** (Authentication + Firestore). No build tools,
no npm install — just paste your Firebase config and go live.

Colour theme: **Purple (`#5B2A86` / `#2E1650`) and White**, matching the school identity.

What actually works out of the box: login, role-based dashboards, student records,
classes/subjects/sessions/grading setup, teacher result entry with automatic
total/grade calculation, Principal approve → publish workflow, manually-typed
teacher & Principal remarks (never auto-generated), the public Result Checker with
PIN + QR verification, printable/PDF report cards, and an audit log.

---

## ⚡ Fastest path to "live" (about 10 minutes)

### Step 1 — Create the Firebase project
1. Go to https://console.firebase.google.com → **Add project** → name it (e.g. `purity-kids-academy`) → create.

### Step 2 — Turn on Authentication
1. In the left menu: **Build → Authentication → Get started**.
2. Enable the **Email/Password** sign-in provider.

### Step 3 — Turn on Firestore
1. **Build → Firestore Database → Create database**.
2. Start in **Production mode** (we supply proper security rules below).
3. Pick any location close to you.

### Step 4 — Get your web config
1. Project **⚙️ Settings → General → Your apps → </> (Web)**.
2. Register the app (any nickname), skip hosting for now.
3. Copy the `firebaseConfig` object shown.
4. Open `index.html` in a text editor, find this block near the top of the `<script>`
   section, and paste your values in:

```js
const firebaseConfig = {
  apiKey: "PASTE_API_KEY_HERE",
  authDomain: "PASTE_PROJECT_ID.firebaseapp.com",
  projectId: "PASTE_PROJECT_ID",
  storageBucket: "PASTE_PROJECT_ID.appspot.com",
  messagingSenderId: "PASTE_SENDER_ID",
  appId: "PASTE_APP_ID"
};
```

### Step 5 — Apply the security rules
1. **Firestore Database → Rules** tab.
2. Delete the default contents and paste in everything from `firestore.rules`
   (included in this download).
3. Click **Publish**.

### Step 6 — Create your first Admin account
The app lets an Admin create every other account, but the very first Admin has to be
made by hand once:
1. **Authentication → Users → Add user** → enter an email + password for yourself.
2. Copy the new user's **User UID** (shown in the users list).
3. **Firestore Database → Start collection** → collection ID: `users` → Document ID:
   *paste the UID you copied* → add these fields:
   | Field | Type | Value |
   |---|---|---|
   | name | string | Your Name |
   | email | string | the email you used |
   | role | string | `admin` |
   | assignments | array | *(leave empty)* |
4. Save.

### Step 7 — Run it
**Instant option (no install):** just double-click `index.html` to open it in a
browser and log in with the admin email/password from Step 6.

**Live public link (recommended, still fast):**
```bash
npm install -g firebase-tools     # one-time
firebase login
cd purity-kids-academy
firebase deploy --only firestore:rules,hosting
```
Firebase prints a live URL like `https://your-project.web.app` — that's your portal,
online immediately. (`.firebaserc` in this folder already points at the project ID —
just replace `PASTE_PROJECT_ID` inside it, or run `firebase use --add` and pick your project.)

### Step 8 — First login checklist
Once logged in as Admin:
1. **School Settings** — confirm/edit school details.
2. **Sessions & Terms** — add e.g. `2025/2026`.
3. **Classes** — add your classes (or click **Load Demo Data** on the dashboard to
   instantly get sample classes, subjects, a grading scale and one demo student per
   section to explore the whole flow).
4. **Subjects** — add subjects and assign them to classes.
5. **Grading Scale** — add grade bands (e.g. 70–100 = A = Excellent).
6. **Students** — add students (a PIN and admission number are generated automatically).
7. **Staff & Users** — create Teacher/Principal accounts, then click **Assign** to
   give a teacher a class + subject.

Then, as a Teacher: **Enter Results** → scores auto-calculate totals & grades →
**Submit**. As Principal/Admin: **Approve & Publish** → add the manually-typed
Teacher/Principal remarks → **Approve** → **Publish**. Published results instantly
appear on the public **Result Checker** (admission number + PIN) with a scannable
QR verification code on the printed report card.

---

## File list
- `index.html` — the entire application (HTML + CSS + JS).
- `firestore.rules` — security rules enforcing who can read/write what.
- `firebase.json`, `.firebaserc` — one-command Firebase Hosting deploy config.

## Notes & honest limitations
- This is a real, working system, not a mockup — every button listed performs a
  genuine Firestore read/write. To keep setup to "paste config and go", it skips
  Cloud Functions; account creation for staff instead uses a second, isolated
  Firebase Auth session in the browser (a standard, safe technique) so the Admin
  isn't logged out when adding a Teacher.
- Nursery/Primary psychomotor & character grids, promotion rules, CSV import/export,
  and Bursar/Counsellor modules from the full spec are intentionally left as a next
  iteration — the core academic pipeline (setup → enter → approve → publish → check →
  print/PDF) is fully wired so you can test and extend it. Ask me for any of these
  next and I'll add them into the same file.
- For a school actually going live with real student data, also consider: Firebase
  App Check (bot protection on the public Result Checker), and tightening the
  `publicResults` rule further once you've tested the flow.
