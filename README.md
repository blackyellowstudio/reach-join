# join.reachapp-people.com

The page Reach invite links and QR codes point to. WhatsApp and most apps only make
`https://` links tappable, so invites can't be `reach://` links alone.

- `/.well-known/apple-app-site-association`: lets iOS open Reach directly when a
  `https://join.reachapp-people.com/connect?...` link is tapped or scanned, with no
  browser step. Its app ID is `4SHU2LK45Y.com.blackyellow.studio.reach`.
- `/connect?token=…&n=Name`: shown only when Reach isn't installed, or the phone
  didn't hand the link to the app. Offers Open in Reach (`reach://connect?token=…`)
  and Get Reach on the App Store.
- `_headers`: serves the association file as JSON (Cloudflare Pages and Netlify read it).
- `icon.png`, `apple-touch-icon.png`, `favicon.png`: the app icon, so a shared link shows
  Reach's icon in WhatsApp and iMessage previews.
- `index.html`, `404.html`: send anything else on this host to www.reachapp-people.com.

Reach checks that the association file is reachable before it shares an `https` link,
and falls back to `reach://` until it is, so nothing breaks before this is deployed.

## Deploy (GitHub Pages)
Published from the `blackyellowstudio/reach-join` repository, branch `main`, root folder.
- `.nojekyll` keeps GitHub from skipping `.well-known/`.
- `CNAME` names the custom domain, `join.reachapp-people.com`.
- DNS: a CNAME record `join` → `blackyellowstudio.github.io`, where the domain's DNS
  is managed. Then, in the repository's Settings → Pages, tick Enforce HTTPS.
- Check: `https://join.reachapp-people.com/.well-known/apple-app-site-association`
  shows the JSON, with no redirect.

To update the site, copy this folder's files into that repository and push.

## Banning someone
`banned.json` lists people removed from Reach, as hashes of their Reach ID (the ID is in
every report email). Every phone reads it within a few hours and treats them as blocked
by everyone; their own Reach shows "account suspended".

    printf 'reach-ban-v1:<Reach ID>' | shasum -a 256

Add the hash (lowercase hex) to `banned`, copy the file into the site repository, push.
