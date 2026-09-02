# EmoStream Hub – R2 Cloud Stream Player & Studio

This package is a responsive web player for public Cloudflare R2 audio and video URLs.

## Included features

- Full-site Firebase Email/Password account gate (all functions are locked before sign-in)
- Primary manager restriction for `hayashirika05@gmail.com`
- Additional Manager Email/Gmail assignment by the primary manager only
- Allowlisted managers must register and sign in with their own verified Firebase password
- Search and genre filters
- Audio and video playback, seek, volume, mute, next/previous, shuffle and repeat
- Favorites and named playlists
- Manager Studio for publishing R2 media URLs
- Computer audio/video upload to Firebase Storage with IndexedDB fallback
- Automatic 12-second inactive-connection fallback prevents uploads from hanging
- Visible upload status and cancel-upload control
- Automatic MP4/WEBM/MOV video detection for uploaded files and R2 URLs
- Local and Firestore catalogs are merged so previously uploaded entries are not hidden after synchronization
- Playlist items always use the full playlist queue, advance automatically, and loop from the last item back to position 1
- Auto-Loop always starts at playlist position 1; single-item playlists restart correctly instead of pausing
- Uploaded catalog metadata synchronized through Firestore
- Favorites and playlists synchronized per signed-in Manager account
- Manager avatar upload from PNG, JPEG, WEBP or GIF files
- Manager avatar synchronized through Firebase Storage and Firestore
- Individual media deletion and playlist deletion
- Firestore synchronization with automatic localStorage fallback
- Cyberduck and Cloudflare R2 setup guide

## Quick start

Open `index.html` in Chrome or upload the whole folder to GitHub Pages, Netlify, Firebase Hosting, or another static host.

## Firebase setup

1. Open Firebase Console → Authentication → Sign-in method.
2. Enable **Email/Password** and optionally **Anonymous** sign-in.
3. Create a Firestore database.
4. Deploy the included `firestore.rules` and `storage.rules` files.
5. Register or sign in as `hayashirika05@gmail.com`.

The site works locally without Firestore, but cloud synchronization and secure shared administration require Firebase Authentication and these Firestore rules.

## Cloudflare R2

The website now uploads computer media through the included Cloudflare Worker and an R2 binding. It never places an R2 Access Key or Secret Key in browser code. The Worker also serves byte-range responses so video seeking works.

### Deploy the R2 upload Worker

1. Create an R2 bucket in the Cloudflare dashboard, for example `emostream-media`.
2. Open `cloudflare-worker/wrangler.toml` and replace `CHANGE_TO_YOUR_R2_BUCKET_NAME` with that exact bucket name.
3. Confirm `ALLOWED_ORIGIN` is the exact origin of the website, such as `https://akarenka.github.io` (do not add the repository path).
4. In PowerShell, enter the Worker folder and run:

```powershell
npx wrangler login
npx wrangler secret put UPLOAD_TOKEN
npx wrangler deploy
```

5. When prompted for `UPLOAD_TOKEN`, enter a new long random password created only for this Worker. Do not use a Cloudflare or Gmail password.
6. Copy the deployed `https://...workers.dev` URL.
7. In EmoStream Manager Studio, paste the Worker URL and the same upload token, then click **Test R2 connection**.

The upload token is stored only for the current browser tab. Uploaded media receives a Worker `/media/...` URL and does not require making the R2 bucket public.

Cloudflare Workers Free and Pro requests currently accept bodies up to 100 MB, so the included configuration limits uploads to 95 MB. Larger files require multipart upload or a higher Cloudflare plan.
