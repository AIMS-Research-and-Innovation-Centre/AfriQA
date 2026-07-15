# AfriQA 2026 Deployment Guide

This guide deploys the website through GitHub Pages and the portal backend
through Google Apps Script.

## Production resources already created

| Item | Value |
| --- | --- |
| Google Sheet database | https://docs.google.com/spreadsheets/d/11L63X0S7ulgu8-fAKztx4h8Awn69seew3oD32rVIm6k/edit |
| `SPREADSHEET_ID` | `11L63X0S7ulgu8-fAKztx4h8Awn69seew3oD32rVIm6k` |
| Google Drive upload folder | https://drive.google.com/drive/folders/1_wQXNC22JrldZRbxg7t2j5LdWcE4jxc5 |
| `UPLOAD_FOLDER_ID` | `1_wQXNC22JrldZRbxg7t2j5LdWcE4jxc5` |

## Part 1: Deploy the website directly on GitHub

Do this first. Do not connect Apps Script yet.

### 1. Create the repository

1. Go to https://github.com/new.
2. Repository name: `afriqa-2026-conference`.
3. Visibility: choose `Public` if you want GitHub Pages to be public.
4. Do not add a README, `.gitignore`, or license from GitHub. The package already includes the needed files.
5. Click `Create repository`.

### 2. Upload the website files

1. In the empty repository, click `uploading an existing file`.
2. Open this local folder:

```text
C:\Users\Cecil Ouma\Documents\Codex\2026-07-15\explore\outputs\afriqa-quantum-conference-site
```

3. Drag these items into the GitHub upload area:
   - `index.html`
   - `assets`
   - `google-apps-script`
   - `README.md`
   - `DEPLOYMENT_GUIDE.md`
   - `REFINED_PROMPT.md`
   - `.nojekyll`
   - `.github`
4. Commit message: `Deploy AfriQA 2026 conference website`.
5. Click `Commit changes`.

If GitHub or Windows does not show hidden files/folders:

1. In the repo, click `Add file` > `Create new file`.
2. Name the file `.nojekyll`.
3. Leave it empty and commit it.
4. Click `Add file` > `Create new file`.
5. Name the file `.github/workflows/pages.yml`.
6. Paste the contents from `.github/workflows/pages.yml` in this package.
7. Commit it.

### 3. Turn on GitHub Pages

1. In the GitHub repository, open `Settings`.
2. Open `Pages`.
3. Under `Build and deployment`, set `Source` to `GitHub Actions`.
4. Open the `Actions` tab.
5. Wait for `Deploy static site to GitHub Pages` to finish successfully.
6. Copy the Pages URL. It will usually look like:

```text
https://YOUR_ORG_OR_USER.github.io/afriqa-2026-conference/
```

Stop here once the GitHub Pages website is live. Then continue to Apps Script.

## Part 2: Deploy the Apps Script backend

1. Open https://script.google.com/.
2. Create a new project named `AfriQA 2026 Portal Backend`.
3. Open Project Settings and enable "Show appsscript.json manifest file".
4. Replace the default `Code.gs` with `google-apps-script/Code.gs`.
5. Replace the manifest with `google-apps-script/appsscript.json`.
6. In Project Settings > Script Properties, add:

| Property | Value |
| --- | --- |
| `SPREADSHEET_ID` | `11L63X0S7ulgu8-fAKztx4h8Awn69seew3oD32rVIm6k` |
| `UPLOAD_FOLDER_ID` | `1_wQXNC22JrldZRbxg7t2j5LdWcE4jxc5` |
| `ADMIN_EMAILS` | The reviewer/admin emails, comma-separated |

7. Run `setupDatabase()` once from the Apps Script editor and approve the requested scopes.
8. Select Deploy > New deployment.
9. Choose type: `Web app`.
10. Set "Execute as" to `Me`.
11. Set access:
    - Use `Anyone` for a public conference registration portal.
    - Use organisation-only access for internal testing.
12. Deploy and copy the Web App URL ending in `/exec`.

## Part 3: Connect the website to Apps Script

1. Open `assets/app.js`.
2. Replace:

```js
appsScriptUrl: "PASTE_DEPLOYED_APPS_SCRIPT_WEB_APP_URL_HERE",
```

with:

```js
appsScriptUrl: "https://script.google.com/macros/s/YOUR_DEPLOYMENT_ID/exec",
```

3. Commit and push the change to GitHub:

```bash
git add assets/app.js
git commit -m "Connect production Apps Script endpoint"
git push
```

4. Wait for the GitHub Pages workflow to redeploy.

## Part 4: Production launch checklist

- Open the GitHub Pages URL.
- Confirm the AfriQA logo appears in the header.
- Create an account using an admin email listed in `ADMIN_EMAILS`.
- Submit a registration with one of:
  - Fully sponsored
  - Partially sponsored
  - Self sponsored
- Save a draft registration and confirm a row appears in the `Applications` tab.
- Submit an abstract and confirm an email notification is sent.
- Upload a small PDF and confirm it appears in the Drive upload folder.
- Open Admin Review, search the applicant, advance status, and export CSV.
- Confirm no demo/mock applicant records appear anywhere.

## Notes

- The portal is production-only. If the Apps Script endpoint is not configured,
  forms show a configuration error and do not create fake submissions.
- The Apps Script URL is visible in browser source because GitHub Pages is static.
  Access control and validation must therefore happen inside Apps Script.
- For stronger production security, add Google Workspace sign-in restrictions,
  rate limiting, and reCAPTCHA before public launch.
