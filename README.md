# SonarBangla BhorOS — AgunPakhi

**Edition:** Firebase Edition  
**Project:** SonarBangla BhorOS  
**Codename:** AgunPakhi

AgunPakhi is the Firebase-oriented edition of SonarBangla BhorOS, a browser-based desktop/console-style environment created by SonarBangla. It is designed to bring account sign-in and cloud-backed user data to the BhorOS experience while retaining local functionality.

> AgunPakhi is a SonarBangla project. It is not an official Nintendo product and is not affiliated with or endorsed by Nintendo.

## Features

- Firebase JavaScript SDK integration (the current build uses SDK v13.1.0).
- Firebase Email/Password authentication when enabled in the Firebase Console.
- Cloud Firestore integration for user profile and supported preference/game metadata.
- Local settings and game storage, with JSON export/import for portable user data.
- eDokan **More Games** entry that opens `https://sonarbangla-edokan.edgeone.dev/` separately, avoiding iframe framing restrictions.
- Mii avatar stage mode configured as `body_inv_depth_mask` in the current patched build.
- Offline/local fallback behavior for data that cannot be loaded from cloud services.

## Firebase project setup

The current project configuration is intended for the Firebase project with ID `sonarbangla-bhoros`. Confirm that this is the correct project in the Firebase Console before deploying.

1. Open the [Firebase Console](https://console.firebase.google.com/) and select `sonarbangla-bhoros`.
2. Under **Authentication**, enable the **Email/Password** sign-in provider.
3. Under **Authentication → Settings → Authorized domains**, add the hostname where AgunPakhi will be hosted.
4. Create a **Cloud Firestore** database if one does not exist.
5. Review and publish the Firestore rules provided with the deployment package. Rules must match the actual paths used by the app and must restrict each user to their own data.
6. Host the site over HTTPS, then test account creation, sign-in, profile sync, sign-out, and sign-in again.

### Deploy with Firebase Hosting

The following commands assume Node.js is installed and the Firebase Hosting configuration files are in the current project directory:

```bash
npm install -g firebase-tools
firebase login
firebase projects:list
firebase deploy --only hosting,firestore:rules
```

Check the project selected by the CLI before deployment. The CLI should show a Hosting URL after a successful deployment, typically under `web.app` and/or `firebaseapp.com`.

AgunPakhi can also be hosted on another static host, but Firebase services still need to be configured in the Firebase Console. When hosting elsewhere, add that host's domain to Firebase Authentication's authorized domains.

## Cloud data and local portability

The current implementation supports cloud sync for the user profile, preferences/settings, installed-game metadata, and recent-game information as implemented in the build. **Do not assume that all local data is automatically uploaded.** Full local game files and gameplay save-state contents may remain on the device; use BhorOS's JSON data export/import to move supported local data between installations.

Keep Firestore Security Rules enabled. Do not replace them with public read/write rules. Test using a separate account before relying on cloud data.

## Troubleshooting

- **“Missing or insufficient permissions”**: check that Firestore rules have been published to the correct Firebase project and that the user is signed in. Verify the document path used by the running build matches the rule paths.
- **Sign-in does not work**: enable Email/Password, verify the authorized domain, and inspect the Firebase Console's Authentication users list.
- **Cloud data is empty**: verify the signed-in UID, project ID, database selection, and network requests in browser developer tools.
- **External game assets fail to load**: check network connectivity and the remote asset host. Firebase configuration does not fix third-party network or CORS failures.
- **Audio does not start automatically**: browsers generally require a user gesture before starting Web Audio. Interact with the page and check the browser's audio settings.

## Project files

The deployment package should include the current BhorOS HTML as the Hosting site's `index.html`, Firebase Hosting configuration, and the reviewed Firestore rules file. Keep these files together when deploying.

## Status

This README documents the intended setup and current implementation scope. A successful local syntax check does not prove that live Firebase authentication, Firestore permissions, or Hosting deployment have been tested. Verify those services in the target Firebase project after deployment.
