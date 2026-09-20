# Weekly Planner — setup

Four files make up the app: `index.html`, `manifest.webmanifest`, `sw.js`, and the three PNG icons.
They must be served together over HTTPS from a single folder. Opening `index.html` by
double-clicking it will not work — service workers and Google sign-in both refuse to run
from `file://`.

## 1. Put the files online

Any static host works. Two easy ones:

**Netlify Drop** — go to app.netlify.com/drop and drag the whole folder onto the page.
You get a URL like `https://something-random.netlify.app` in about ten seconds. No account
needed to start, though making the URL permanent requires a free account.

**GitHub Pages** — create a repo, push these files, then in Settings → Pages set the source
to your main branch. Your URL will be `https://<username>.github.io/<repo>/`.

Note the URL. You need it in step 2.

## 2. Get a Google OAuth client ID

Go to console.cloud.google.com and sign in with the Google account whose Drive you want to use.

1. **Create a project.** Top-left project dropdown → New Project. Name it anything.
2. **Enable the Drive API.** APIs & Services → Library → search "Google Drive API" → Enable.
3. **Configure the consent screen.** APIs & Services → OAuth consent screen.
   - User type: External. (Internal is only for Workspace orgs; if UCF gives you that option
     and you're using your UCF account, Internal is simpler — no test-user step.)
   - Fill in app name, your email for both support and developer contact. Save.
   - On the Test users step, add your own Google address. Save.
   - Leave the app in Testing status. You do not need to publish or get verified for personal use.
4. **Create the credential.** APIs & Services → Credentials → Create Credentials →
   OAuth client ID → Application type: **Web application**.
   - Under *Authorized JavaScript origins*, add your URL's origin with no path and no
     trailing slash — for example `https://something-random.netlify.app`
     or `https://username.github.io`.
   - Create. Copy the client ID. It looks like `1234567890-abc123def456.apps.googleusercontent.com`.

## 3. Connect

Open your hosted URL, click **Connect Google Drive**, paste the client ID, and approve the
Google consent screen. Google will warn that the app isn't verified — that's expected for a
personal app in Testing status. Click Advanced → Continue.

The planner creates `planner-data.json` in the root of your Drive. You can move it into a
folder afterward; the app tracks it by ID, not location.

## 4. Install it on your phone

Open the same URL in Chrome on Android → menu → **Add to Home screen** (or "Install app").
Paste the client ID once on the phone too, since that's stored per device. After that it
opens full screen with its own icon.

On the Mac, Chrome shows an install icon in the address bar; Safari uses File → Add to Dock.

## Things worth knowing

- **The access token lasts about an hour.** Browser-only OAuth can't hold a refresh token,
  so after a long gap you'll see "Google session expired" and need to tap Connect again.
  It's one tap with no password, because Google remembers the consent.
- **Offline is fine.** Tasks save to the device immediately and sync when you're back online
  or when you switch back to the tab.
- **Conflicts resolve per task, not per file.** Each task carries a timestamp, and the newer
  edit wins. Editing different tasks on two devices merges cleanly. Editing *the same* task
  on both while offline keeps only the later edit.
- **Deleted tasks leave a tombstone for 30 days** so a delete on one device doesn't get
  resurrected by the other, then they're cleared out.
- **The `drive.file` scope** means the app can only see files it created itself. It cannot
  read the rest of your Drive, which is also why the consent screen asks for so little.
