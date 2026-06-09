# Sir Blox Documentation Synchronization Pipeline

An autonomous DevOps pipeline that monitors the public Qblox documentation servers, strips out boilerplate structural layout data, and live-synchronizes a master text corpus across a 3-part Google Doc framework for engineering team access.

## How to Deploy Your Own Instance 

### Step 1: Clone the Repository
```bash
git clone [https://github.com/YOUR_USERNAME/Sir-Blox.git](https://github.com/YOUR_USERNAME/Sir-Blox.git)
cd Sir-Blox

```
### Step 2: Enable Google Cloud Platform (GCP) Credentials
1. Go to the Google Cloud Console.
2. Create a new project, then search for and enable the "Google Drive API" and the "Google Docs API".
3. Configure your OAuth Consent Screen, set the application type to Desktop, and download your authentication client file. Save it into your project folder exactly as "credentials.json".
4. Open and run the Jupyter notebook locally on your computer exactly once to complete the initial browser authentication handshake. This process will generate your local user authorization file named "token.json".

### Step 3: Configure GitHub Infrastructure Secrets
To allow the headless GitHub Actions cloud runner to connect securely to your accounts without hardcoding private keys, go to your GitHub Repository web page. Navigate to Settings, then click on Secrets and variables, select Actions, and add the following two Repository Secrets:

Name: GOOGLE_CREDENTIALS_JSON
Value: Paste the entire raw text contents of your local credentials.json file.

Name: GOOGLE_TOKEN_JSON
Value: Paste the entire raw text contents of your local token.json file.

# PIPELINE AUTOMATION LIFECYCLE
* Automated Trigger: The workflow wakes up completely autonomously via GitHub Cron at 06:00 UTC every single day.
* Manual Trigger: The pipeline can be manually executed at any time by navigating to the Actions tab on your GitHub repository interface and clicking the "Run workflow" button.
