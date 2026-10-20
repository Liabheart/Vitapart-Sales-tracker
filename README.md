# Vitaparts Operations: Vercel deployment

This project contains:
- the platform page (`public/index.html`);
- one server function (`api/index.js`, with its logic in `lib/core.js`);
- the code that sends emails through your company mailbox.

Data is stored in **Upstash Redis**, which you add from inside Vercel on the free plan.

## What you need
1. A **Vercel** account (vercel.com; the free Hobby plan is enough).
2. A **GitHub** account. This is the easiest way to get the files into Vercel.
3. The **SMTP details of the mailbox that will send the emails**, for example sales.vitaparts@vitafoam.com.ng. Ask your IT team for the host, port, username and password. Common settings:

| Mail provider | SMTP_HOST | SMTP_PORT | Password to use |
|---|---|---|---|
| Microsoft 365 / Outlook | smtp.office365.com | 587 | Mailbox password (IT must enable *Authenticated SMTP* for that mailbox) |
| Google Workspace / Gmail | smtp.gmail.com | 465 | An *App password* (needs 2-Step Verification on the account) |
| Other hosts (cPanel, Zoho, etc.) | from your provider | 465 or 587 | Mailbox password |

## Step 1. Put the files on GitHub
1. On github.com, click **New repository** and name it `vitaparts-operations`. Choose **Private**, then click **Create**.
2. Click **uploading an existing file**.
3. Unzip the package and drag in everything inside it, keeping the folders: `api`, `lib`, `public`, `package.json`, `vercel.json`, `.gitignore` and `README.md`.
4. Click **Commit changes**.

## Step 2. Import into Vercel
1. On vercel.com, click **Add New → Project** and import the `vitaparts-operations` repository.
2. Leave **Framework Preset** as *Other*. Leave the build settings empty.
3. Open **Environment Variables** and add these (`.env.example` lists them too):

| Name | Value |
|---|---|
| `APP_PASSWORD` | A password your team will type to open the platform |
| `SMTP_HOST` | e.g. `smtp.office365.com` |
| `SMTP_PORT` | e.g. `587` |
| `SMTP_USER` | e.g. `sales.vitaparts@vitafoam.com.ng` |
| `SMTP_PASS` | That mailbox's password or app password |
| `MAIL_FROM` | The address emails come from, usually the same as `SMTP_USER` |

4. Click **Deploy**.

## Step 3. Add the database
1. In the project, open the **Storage** tab, then **Create Database** or **Browse Marketplace**.
2. Choose **Upstash → Redis** and the free plan, then connect it to this project.
3. Vercel adds `KV_REST_API_URL` and `KV_REST_API_TOKEN` (or `UPSTASH_REDIS_REST_URL` and `UPSTASH_REDIS_REST_TOKEN`) automatically. The code accepts either pair.
4. Go to **Deployments**, open the menu (**⋯**) on the latest deployment and click **Redeploy** so the new settings take effect.

## Step 4. Test
1. Open the site address Vercel gives you, e.g. `vitaparts-operations.vercel.app`, and enter the access password.
2. Create a test sales order and check that all six recipients get the email.
3. Delete the test order with **Edit → Delete order**.
4. If an email fails, the order still saves and shows **Email failed** with the reason. The usual cause is wrong SMTP settings. Fix the variables, redeploy, and press **Resend**.

## Using your own web address (optional)
To use an address like `ops.vitaparts.com.ng`, go to **Settings → Domains** in Vercel and add the domain. Vercel shows the DNS record your IT team needs to add.

## Making changes later
Edit a file on GitHub and commit. Vercel redeploys automatically within a minute.

## Good to know
- **Who can get in:** everyone shares the single access password. To change it, update `APP_PASSWORD` in Vercel and redeploy. Everyone then signs in again.
- **Recipients:** change them in the **Email updates** tab. The six defaults are in `lib/core.js`.
- **SKUs and CSEs:** these lists are at the top of the script in `public/index.html` (`SKUS` and `CSES`).
- **Email limits:** these depend on your mailbox. Microsoft 365 allows 10,000 recipients a day, and Google Workspace about 2,000 through SMTP. Either is ample.
- **Refresh:** the page updates every 30 seconds and whenever someone returns to the tab.
