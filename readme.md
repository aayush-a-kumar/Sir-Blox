# Sir Blox Documentation Synchronization Pipeline

Sir Blox is an automated documentation synchronization pipeline for Qblox documentation.

It monitors the public Qblox documentation, extracts the relevant technical content, removes boilerplate/layout text, and synchronizes the cleaned corpus into a three-part Google Docs knowledge base for easier engineering team access.

## What This Does

Sir Blox:

- Downloads the latest Qblox documentation content
- Cleans and strips unnecessary page structure
- Splits the resulting text into multiple Google Docs
- Updates the docs automatically through GitHub Actions
- Skips unnecessary updates when the source documentation has not changed

The generated Google Docs are intended to be the user-facing output. Most people in the office should only need access to those docs, not the code itself.

## Repository Structure

```text
Sir-Blox/
├── .github/
│   └── workflows/
│       └── run_sir_blox.yml
├── sir_blox_v0.3.ipynb
├── requirements.txt
└── README.md
```

## Requirements

You need:

- Python 3.11 or later
- Git
- A GitHub account
- A Google account
- Access to Google Cloud Console
- Google Drive API enabled
- Google Docs API enabled

Python dependencies are listed in `requirements.txt`:

```text
requests
beautifulsoup4
google-auth-oauthlib
google-api-python-client
nbconvert
```

## Deployment Overview

The intended deployment flow is:

1. Clone the repository.
2. Create Google Cloud OAuth credentials.
3. Run the notebook once locally to generate a Google authorization token.
4. Store the credentials and token as GitHub Actions secrets.
5. Let GitHub Actions run the pipeline automatically.

## Step 1: Clone the Repository

```bash
git clone https://github.com/aayush-a-kumar/Sir-Blox.git
cd Sir-Blox
```

## Step 2: Create a Python Environment

On macOS or Linux:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

On Windows PowerShell:

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
```

Then install dependencies:

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

## Step 3: Set Up Google Cloud Credentials

1. Go to the Google Cloud Console.
2. Create a new Google Cloud project.
3. Enable the following APIs:
   - Google Drive API
   - Google Docs API
4. Configure the OAuth consent screen.
5. Create OAuth client credentials.
6. Choose **Desktop app** as the application type.
7. Download the OAuth client JSON file.
8. Rename the file exactly:

```text
credentials.json
```

9. Place `credentials.json` in the root folder of this repository.

Do **not** commit `credentials.json` to GitHub.

## Step 4: Run the Pipeline Once Locally

The first local run is required to complete Google OAuth login in the browser and generate `token.json`.

Convert the notebook to a Python script and run it:

```bash
jupyter nbconvert --to script sir_blox_v0.3.ipynb
python -u sir_blox_v0.3.py
```

During this step, a browser window should open asking you to approve access to Google Drive and Google Docs.

After successful authentication, the repository folder should contain:

```text
credentials.json
token.json
```

Again, do **not** commit either of these files.

## Step 5: Add GitHub Actions Secrets

In GitHub, open the repository and go to:

```text
Settings → Secrets and variables → Actions → New repository secret
```

Add the following two repository secrets.

### Secret 1: Google Credentials

Name:

```text
GOOGLE_CREDENTIALS_JSON
```

Value:

Paste the full contents of your local `credentials.json` file.

### Secret 2: Google Token

Name:

```text
GOOGLE_TOKEN_JSON
```

Value:

Paste the full contents of your local `token.json` file.

## Step 6: Run the Pipeline Manually

In GitHub, go to:

```text
Actions → Run Sir Blox Synchronization Pipeline → Run workflow
```

The workflow will:

1. Check out the repository.
2. Install Python.
3. Install dependencies from `requirements.txt`.
4. Reconstruct `credentials.json` and `token.json` from GitHub Secrets.
5. Convert the notebook to a Python script.
6. Run the synchronization pipeline.
7. Create or update the Sir Blox Google Docs.

## Step 7: Confirm Output

After the workflow finishes, check Google Drive for the generated documents:

```text
Sir_Qblox_Master_Docs_Part1
Sir_Qblox_Master_Docs_Part2
Sir_Qblox_Master_Docs_Part3
```

These documents are the main user-facing output of Sir Blox.

## Automatic Runs

The GitHub Actions workflow is configured to run automatically once per day.

You can also trigger it manually from the GitHub Actions tab.


## Intended Office Use

For most office users, the recommended workflow is simple:

1. The maintainer deploys and manages the GitHub Actions pipeline.
2. The pipeline updates the Google Docs automatically.
3. Office users access the generated Google Docs directly.

Most users do **not** need to clone the repository, install Python, or run the notebook locally.


## Files That Should Not Be Committed

Do not commit local credentials, tokens, downloaded documentation, or generated artifacts.

Recommended `.gitignore` entries:

```gitignore
# Python
__pycache__/
*.py[cod]
.venv/
venv/

# Jupyter
.ipynb_checkpoints/

# macOS
.DS_Store

# Google OAuth credentials
credentials.json
token.json

# Generated files
raw_download/
qblox_lean_docs_latest.txt
sir_blox_v0.3.py
```

## Troubleshooting

### The GitHub Action Fails During Authentication

Regenerate `token.json` locally by running the notebook/script again, then update the `GOOGLE_TOKEN_JSON` GitHub secret.

### The Google Docs Are Not Updating

Check the GitHub Actions logs.

If the logs say the documentation marker has not changed, then the pipeline intentionally skipped the update.

If the logs show an authentication or permissions error, refresh the Google OAuth token and update the GitHub secret.

### The Output Docs Were Deleted

Run the GitHub Action manually. The pipeline should recreate the output documents if it has the correct Google Drive permissions.

### The Notebook Filename Changed

Update the GitHub Actions workflow so the `nbconvert` command points to the correct notebook file.

For example:

```bash
jupyter nbconvert --to script your_new_notebook_name.ipynb
python -u your_new_notebook_name.py
```

## Maintainer Notes

This repository is currently notebook-driven. That is fine for prototyping and internal use, but a cleaner production version would eventually move the main logic into a normal Python script or package.
