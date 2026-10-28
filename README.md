# HRVELLY — released Android apps

Where every company's HRVELLY server gets the Android apps it offers to phones and reception tablets.
Nothing is built here, and the branch holds only this file: each build is a
[GitHub Release](https://github.com/mkn19042000/hrvelya-releases/releases), and the **latest** release is the
one in use.

## What a release holds

Every release carries both apps, so that "latest" always has both:

| File | What it is |
|---|---|
| `hrvelly-employee.apk` | HRVELLY, the employee app (`in.hrvelly.employee`) |
| `version.json` | Its version, minimum version and "what's new" |
| `hrvelly-kiosk.apk` | HRVELLY Kiosk, the reception tablet app (`in.hrvelly.kiosk`) |
| `kiosk-version.json` | Its version, minimum version and "what's new" |
| `com.hrvelya.app.apk` | A copy of the employee APK under the retired 1.0.x app's file name, so phones still on it are offered the new app |

Each app has its **own version**. A release raises only the app that changed; the other app's APK and
manifest are copied over from the previous release unchanged, so its phones or tablets are not offered
anything. The release is named after what it built: "HRVELLY 2.0.4", "HRVELLY Kiosk 2.0.4", or both.

## How releases get here

Automatically. A push to `main` in [hrvelly-mobile](https://github.com/mkn19042000/hrvelly-mobile) that
changes `employee-app/` or `kiosk-app/` runs its **Release** workflow: it builds the changed app with the next
version number, signs it, checks it is signed with the same key as the released app, and publishes the new
release here. See *Releasing an update* in that repository's README.

Don't edit or delete a release's APKs by hand. Phones only install an update signed with the key their app
was signed with, and the workflow is what checks that.

## How the apps reach phones

1. Each company's server reads `releases/latest/download/version.json` and `kiosk-version.json`, at most
   10 minutes old (`ManifestCacheMinutes`).
2. It copies the APKs to itself and serves them from its own domain:
   `https://api-<company>.hrvelly.in/download/employee.apk` and `/download/kiosk.apk`.
3. The employee app, the kiosk and the web app's Mobile Apps page ask that server
   (`/api/app/version?app=employee|kiosk`) and download from it. Phones never see GitHub.

## Forcing an update

`minimumVersionCode` in a manifest makes every build below it unusable until updated. The workflow carries
the previous value forward, so raising it is done by hand on the latest release:

```powershell
gh release download --repo mkn19042000/hrvelya-releases --pattern version.json
# edit minimumVersionCode in version.json
gh release upload <tag> version.json --repo mkn19042000/hrvelya-releases --clobber
```

Reserve it for a security fix, or a server change the old build cannot work with. A forced update on a bad
connection is an employee who cannot check in.
