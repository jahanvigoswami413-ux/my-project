# RetainIQ (Firebase version) – setup guide

Real accounts: anyone signs up with their own email + password, the admin (CEO) approves them
and chooses their position, role and data domain. Customer data lives in Firestore and the
database rules stop a user from reading data outside their domain.

## 1. Create the Firebase project
1. Go to https://console.firebase.google.com and click **Add project** (free Spark plan is enough).
2. In the project, click **</> Web** to add a web app. Copy the `firebaseConfig` values.
3. Open `index.html`, find `FIREBASE_CONFIG` and paste your values there.

## 2. Turn on login and the database
1. **Build > Authentication > Get started > Sign-in method > Email/Password > Enable.**
2. **Build > Firestore Database > Create database** (production mode, pick a nearby region).
3. Open the **Rules** tab, paste the contents of `firestore.rules`, click **Publish**.

## 3. Run or host it
Browsers block sign-in from a plain `file://` page, so use one of these:
- Local test: in this folder run `python3 -m http.server 8000`, open http://localhost:8000
  (localhost is already an authorised domain).
- Hosting: Firebase Hosting (`firebase deploy`), Netlify or Vercel. Then add the site's domain under
  **Authentication > Settings > Authorized domains**.

## 4. Make yourself the admin (one time)
1. Open the app, click **Sign in > New here? Create an account**, and register with your admin email
   (e.g. jahanvigoswami413@gmail.com) and a password. It will say "waiting for approval".
2. Firebase console > **Authentication > Users**: copy your **User UID**.
3. **Firestore Database > Start collection** named `admins`, **Document ID** = your UID,
   add any field (e.g. `admin` = true). Save.
4. Sign in again: you are now Admin and see everything.

## 5. Load the data
As admin, either keep the sample data or click **Upload your CSV**, then click
**Save data to database**. (CSV columns: customerID, gender, tenure, Contract, InternetService,
MonthlyCharges, TotalCharges, Churn.)

## 6. Add your team
- A GM/manager opens the site, clicks **Create an account**, fills email, password, name, position,
  department. They see "Waiting for admin approval".
- You sign in, open **Approvals**, click **Review & approve**, choose position, role
  (Manager / Analyst / Viewer) and data domain (All data / a Contract / an Internet service), and confirm.
- They sign in normally with their own email and password and see only their role's pages and domain's data.

## Notes
- If a scoped user gets "permission denied" reading customers, test the rule in the Firestore
  **Rules Playground**; the query must include the same domain filter as the rule (the app does this).
- Scoped users' prediction model is trained only on the data they can read.
- Passwords are handled by Firebase Authentication; the app never stores them.
- Revoking a user blocks their next sign-in and next data read.
