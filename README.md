# Vestates lease dashboard

A free, shared lease dashboard. The page is hosted on **GitHub Pages**; the data and staff sign-in use **Google Firebase** on its free Spark plan. No card is needed for either.

The page itself contains **no tenant data**. Data lives in your Firebase database and only people on the team list can read it.

> Menu names in Firebase and GitHub sometimes move slightly. If a step doesn't match exactly, look for the closest option with the same name.

---

## Part A: Firebase (about 15 minutes)

### 1. Create the project
1. Go to <https://console.firebase.google.com> and sign in with a Google account (use one the company will keep, not a personal one that might be closed).
2. Click **Create a project** (or **Add project**). Name it, e.g. `vestates-lease-dashboard`.
3. When asked about **Google Analytics**, switch it **off**. It isn't needed.
4. Click **Create project**, wait, then **Continue**.

You are on the free **Spark** plan by default. Don't upgrade.

### 2. Register the web app and copy its settings
1. On the project home page, click the **Web** icon `</>` ("Add app").
2. Nickname: `dashboard`. **Don't** tick Firebase Hosting. Click **Register app**.
3. Firebase shows a block of code containing `const firebaseConfig = { apiKey: ..., authDomain: ..., ... }`. Keep this page open; you'll copy those six values in step 6.
   (You can always find them again under ⚙ **Project settings** › **General** › **Your apps**.)

### 3. Create the database and lock it down
1. Left menu: **Build › Firestore Database** › **Create database**.
2. Choose a **location** close to you. *This can't be changed later.*
3. Choose **Start in production mode** › **Create**.
4. Open the **Rules** tab. Delete everything there, paste the full contents of `firestore.rules` from this folder, and click **Publish**.

### 4. Switch on sign-in
1. Left menu: **Build › Authentication** › **Get started**.
2. **Sign-in method** tab:
   - **Google** › Enable › pick a support email › **Save**.
   - **Email/Password** › enable the first switch only › **Save**.

### 5. Make yourself the first admin
1. Go back to **Firestore Database** › **Data** tab › **Start collection**.
2. Collection ID: `members` › Next.
3. Document ID: **your email address in lower case** (the one you'll sign in with).
4. Add fields:
   - `role` · type **string** · value `admin`
   - `name` · type **string** · value *your name*
5. **Save**.

### 6. Put the settings into the page
Open `index.html` in a text editor (Notepad is fine). Search for `FIREBASE_CONFIG`. Replace each `PASTE_...` value with the matching value from step 2, keeping the quotes:

```js
var FIREBASE_CONFIG = {
  apiKey: "AIza...",
  authDomain: "vestates-lease-dashboard.firebaseapp.com",
  projectId: "vestates-lease-dashboard",
  storageBucket: "vestates-lease-dashboard.appspot.com",
  messagingSenderId: "1234567890",
  appId: "1:1234567890:web:abc123"
};
```

These values identify your project; they are **not passwords** and are safe in a public page. The security comes from the rules in step 3 and the team list.

---

## Part B: GitHub Pages (about 10 minutes)

### 7. Upload the page
1. Sign in at <https://github.com> › **New repository**.
2. Name it, e.g. `vestates-dashboard`. Choose **Public** (free GitHub Pages needs a public repository; this is safe because the page holds no data).
3. Click **uploading an existing file** and upload **only**: `index.html`, `firestore.rules`, `README.md`, `.gitignore`.
   **Never upload the backup `.json` or any spreadsheet.** They contain tenant names and phone numbers.
4. **Commit changes**.

### 8. Turn on GitHub Pages
1. In the repository: **Settings › Pages**.
2. Source: **Deploy from a branch**. Branch: `main`, folder `/ (root)` › **Save**.
3. After a minute or two the page shows your address, e.g. `https://yourname.github.io/vestates-dashboard/`.

### 9. Allow that address to sign in
Firebase › **Authentication › Settings › Authorized domains** › **Add domain** › enter `yourname.github.io` (no `https://`, no folder) › **Add**.

---

## Part C: First sign-in and moving the data

### 10. Sign in and load the data
1. Open your GitHub Pages address and **Sign in with Google** using the email from step 5.
2. Scroll to the bottom › **Data, backup and restore** › **Restore from a backup (.json)** › choose `Vestates_Lease_Backup_2026-10-02.json` (the file you downloaded separately).
3. All 99 units, 20 head leases and settings load in. The status dot turns green when everything has saved.

### 11. Add your team
Same panel › **Team access**: enter each colleague's email, choose a role, **Add person**.

| Role | Can do |
|---|---|
| **Viewer** | See everything, download reports. Can't change anything. |
| **Editor** | Everything a viewer can, plus edit leases, renewals and payments. |
| **Admin** | Everything an editor can, plus manage the team list. |

How they sign in:
- **Gmail or Google Workspace address:** they just click *Sign in with Google*.
- **Any other address (e.g. Outlook):** create their login in Firebase › **Authentication › Users › Add user** (email + a temporary password) and send them the password. On first sign-in the dashboard emails them a confirmation link; after that they can use *Forgot your password?* to set their own.

To remove someone, click **Remove** next to them. They lose access immediately.

---

## Updating the page later
If you get a new version of `index.html`, paste your six Firebase settings into it again (step 6) and upload it to the repository, replacing the old file. Your data is not affected.

## Free limits (Spark plan)
Cloud Firestore allows roughly 50,000 reads and 20,000 writes a day, plus 1 GiB of storage. Each time someone opens the dashboard it uses about 150 reads, so a team can open it a few hundred times a day. If the daily allowance is ever used up, saving pauses until the next day. Nothing is charged.

## Backups
Download **Download full backup (.json)** regularly (e.g. weekly) and store it somewhere private. It contains everything, including payments and lease history, and can be restored with step 10.
