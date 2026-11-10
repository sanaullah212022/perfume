# ScentSense — AI-Powered Perfume Analyzer

ScentSense is a beginner-friendly Python + Streamlit application that uses the Gemini Flash API to analyze perfumes and suggest fragrances based on personal preferences.

## Features

- Enter a perfume name and brand.
- Upload a perfume bottle image (JPG, JPEG, PNG, or WEBP).
- Select fragrance family, occasion, season, and budget.
- Generate an organized report covering identification, fragrance profile, notes, longevity, projection/sillage, occasions, seasons, similar perfumes, fragrance personality, and an AI rating.
- Download the report as a Markdown file.
- Handles missing API keys, empty model responses, and API errors.
- Clearly distinguishes user-provided details, model knowledge, estimates, and unknown information.

## Important accuracy note

Gemini does not automatically verify product specifications against a live brand database. This app instructs the model not to invent notes, performance figures, or prices. It labels subjective performance comments as estimates and asks the model to say when information is not reliably known.

Recommendations are suggestions, not a live inventory or price comparison. Always confirm exact notes and specifications with the perfume brand or packaging.

## Project files

```text
perfume_analyzer_app/
├── app.py
├── README.md
└── requirements.txt
```

## 1. Get a Gemini API key

1. Open [Google AI Studio](https://aistudio.google.com/).
2. Sign in with your Google account.
3. Create an API key from the API key section.
4. Copy the key and keep it private. Do not paste it into `app.py` or upload it to GitHub.

The app uses Google's `google-genai` Python SDK and defaults to `gemini-2.5-flash`. You can change the model using the optional `GEMINI_MODEL` secret if your account has access to a different Flash model.

## 2. Run locally (Windows / macOS / Linux)

### Create a virtual environment (recommended)

```bash
python -m venv venv
```

Activate it:

**Windows PowerShell**
```powershell
venv\Scripts\Activate.ps1
```

**macOS / Linux**
```bash
source venv/bin/activate
```

### Install packages

```bash
pip install -r requirements.txt
```

### Add your Gemini API key securely

Create a folder named `.streamlit` beside `app.py`, then create a file named `secrets.toml` inside it:

```text
.streamlit/
└── secrets.toml
```

Put this in `.streamlit/secrets.toml`:

```toml
GEMINI_API_KEY = "paste_your_real_gemini_api_key_here"
GEMINI_MODEL = "gemini-2.5-flash"
```

Replace the example text with your actual API key. Do not add this file to GitHub.

### Start the app

```bash
streamlit run app.py
```

Streamlit will show a local address, usually `http://localhost:8501`.

## 3. Develop and test in Google Colab

Colab is useful for initial experimentation. Streamlit Community Cloud is the recommended final hosting option; you do not need a Cloudflare tunnel for the final deployment.

1. Open [Google Colab](https://colab.research.google.com/) and create a new notebook.
2. Upload `app.py` and `requirements.txt` to the Colab session, or create them with notebook file-writing cells.
3. Install dependencies:

```python
!pip install -r requirements.txt
```

4. Create the secrets file without hardcoding your key into the notebook. Run:

```python
from getpass import getpass
from pathlib import Path

Path(".streamlit").mkdir(exist_ok=True)
key = getpass("Paste your Gemini API key (input is hidden): ")
Path(".streamlit/secrets.toml").write_text(
    f'GEMINI_API_KEY = "{key}"\nGEMINI_MODEL = "gemini-2.5-flash"\n'
)
```

5. Start Streamlit in the background:

```python
!streamlit run app.py --server.port 8501 --server.address 0.0.0.0 > streamlit.log 2>&1 &
```

6. To view the app from a Colab session, you can use a temporary Cloudflare quick tunnel. Download the Cloudflare executable:

```python
!wget -q https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64 -O cloudflared
!chmod +x cloudflared
```

Then start the tunnel:

```python
!./cloudflared tunnel --url http://localhost:8501
```

7. Open the temporary `https://...trycloudflare.com` address printed by the command. Keep the Colab runtime running while testing. The URL is temporary and changes when a new tunnel starts.

**Colab security:** Never print the contents of `secrets.toml`, share the notebook with the key embedded in a cell, or upload `.streamlit/secrets.toml` to GitHub. Colab runtimes are temporary, so the secrets file may need to be recreated after reconnecting.

## 4. Push the project to GitHub

1. Create a new repository on [GitHub](https://github.com/). For example: `perfume-analyzer`.
2. On your computer, put `app.py`, `README.md`, and `requirements.txt` in one project folder.
3. Create a `.gitignore` file in that folder with:

```gitignore
.streamlit/secrets.toml
__pycache__/
*.pyc
venv/
.env
```

4. Open a terminal in the project folder and run:

```bash
git init
git add app.py README.md requirements.txt .gitignore
git commit -m "Create ScentSense perfume analyzer"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/perfume-analyzer.git
git push -u origin main
```

Replace `YOUR-USERNAME` with your GitHub username. If Git asks you to sign in, follow GitHub's authentication prompts. Do not commit your API key.

## 5. Deploy on Streamlit Community Cloud

1. Push the project to GitHub as described above.
2. Open [Streamlit Community Cloud](https://share.streamlit.io/) and sign in with GitHub.
3. Click **Create app**.
4. Select your repository, branch (`main`), and main file path (`app.py`).
5. Open **Advanced settings** (or the Secrets field in the deployment flow).
6. Paste the following TOML, replacing the example key with your real Gemini API key:

```toml
GEMINI_API_KEY = "paste_your_real_gemini_api_key_here"
GEMINI_MODEL = "gemini-2.5-flash"
```

7. Save and click **Deploy**.
8. Wait for deployment. Streamlit will provide a public `*.streamlit.app` URL.
9. If you change the API key later, open your app's settings and update the Secrets section.

## 6. Where exactly do I add the Gemini API key?

- **Local computer:** `.streamlit/secrets.toml`
- **Google Colab:** create `.streamlit/secrets.toml` using the hidden `getpass()` cell above.
- **Streamlit Community Cloud:** app workspace → app menu/settings → **Secrets**, or paste it into **Advanced settings → Secrets** during deployment.

The key name must be exactly `GEMINI_API_KEY`. The code reads it using `st.secrets` and falls back to the `GEMINI_API_KEY` environment variable. Never write your real key directly in `app.py`.

## Troubleshooting

- **API key not found:** Check the spelling `GEMINI_API_KEY`, confirm the secrets file is in `.streamlit/secrets.toml`, and restart Streamlit.
- **Invalid API key / permission error:** Create or copy a valid key from Google AI Studio and check that the selected model is available to your account.
- **Model not found:** Set `GEMINI_MODEL` to a Flash model currently available to your Gemini API account.
- **Quota or rate-limit error:** Check your API usage and limits in Google AI Studio; wait and try again if necessary.
- **Image error:** Use JPG, JPEG, PNG, or WEBP and keep the image under 10 MB.
- **Blank response:** Try again with a clearer perfume name or label image.

## Technology

- Python
- Streamlit
- Google Gen AI Python SDK (`google-genai`)
- Pillow

## Disclaimer

ScentSense is an AI-assisted educational tool. It is not affiliated with perfume brands and does not provide laboratory testing, official authentication, medical advice, or guaranteed product pricing. Fragrance performance varies between people and conditions.
