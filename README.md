# IKAZE THEO AI — Firebase + ChatGPT/OpenAI starter

This package contains the updated `index.html` and a matching Node.js backend.

## What is configured in the HTML

- The Firebase Web App configuration provided for project `ikaze-theo-ai` is already inserted.
- Google sign-in and email/password sign-up/sign-in are wired through Firebase Authentication.
- The first AI provider is ChatGPT/OpenAI.
- The chat sends Firebase ID tokens to the backend and waits for the actual OpenAI API response.
- Conversation history is stored in this browser and has open, pin/unpin, rename and delete controls.
- The Three.js neural network is layered behind the page content and click ripples do not block buttons.
- Studio cards navigate to separate `studios/*.html` routes which you can create later.

## Important: ChatGPT website vs OpenAI API

`https://chatgpt.com/` is the consumer ChatGPT website, not an API URL that a separate website can call to receive answers. This package connects through the official OpenAI Responses API from the server. Do not attempt to scrape or iframe ChatGPT's private website endpoints. The OpenAI API key belongs only in the backend environment, never in `index.html`.

The Firebase `apiKey` included in the page is the Firebase Web App key, not an OpenAI API key. Firebase documents these web keys as public project identifiers; protect your data using Firebase Authentication, Security Rules, authorized domains, API restrictions and (where applicable) App Check.

## 1. Enable Firebase Authentication

1. Open https://console.firebase.google.com/ and select project `ikaze-theo-ai`.
2. Open **Authentication → Sign-in method**.
3. Enable **Google** and **Email/Password**.
4. Under Authentication settings → **Authorized domains**, add `localhost` and `127.0.0.1` for local development, then add your real production domain when deployed.
5. The Firebase web config is already in `index.html`; no replacement is needed unless you registered a different Firebase Web App.

## 2. Create an OpenAI API key

1. Open https://platform.openai.com/api-keys and create an API key.
2. Check that the API project has billing enabled and access to the selected model.
3. Copy the key to `backend/.env` as `OPENAI_API_KEY`. Never put it in this HTML or commit it to GitHub.

A ChatGPT subscription and API billing are separate. API requests may incur usage charges.

## 3. Configure the backend secrets

1. Install Node.js 20 or newer.
2. Copy `backend/.env.example` to `backend/.env`.
3. Set `OPENAI_API_KEY` to the key created above.
4. In Firebase Console → Project settings → Service accounts → Firebase Admin SDK, generate a private key.
5. Keep that downloaded service-account JSON private. Put its complete JSON content into `FIREBASE_SERVICE_ACCOUNT_JSON` in `backend/.env` as a one-line JSON value, preserving escaped `\n` in `private_key`.
6. Keep `.env` and the downloaded service-account file out of GitHub.

The backend uses `gpt-6.2-astra` by default. This is shown in the current official OpenAI API quickstart; change `OPENAI_MODEL` in `.env` if your API project uses another available model.

## 4. Run the backend and page locally

Open a terminal in the `backend` folder:

```bash
npm install
npm run dev
```

The backend should listen at `http://localhost:8787`.

Then serve the root `index.html` with VS Code Live Server (do not open it as a `file://` URL). A common local URL is `http://127.0.0.1:5500`.

1. Click **Log in / Sign up**.
2. Continue with Google or create an account using email/password.
3. Open AI Workspace. **ChatGPT / OpenAI** is the selected provider.
4. Send a question. The send control stays in a waiting state until the backend returns an actual answer.

The HTML defaults to `http://localhost:8787/api/chat`. If you choose a different Live Server port, update `ALLOWED_ORIGINS` in `.env` to contain the exact website origin and restart the backend.

## 5. Deploy

Deploy the `backend` folder to a Node.js hosting service. Add `OPENAI_API_KEY`, `OPENAI_MODEL`, `FIREBASE_SERVICE_ACCOUNT_JSON`, `PORT`, and `ALLOWED_ORIGINS` as environment variables/secrets in the hosting dashboard. Use HTTPS.

Then change `window.IKAZE_CONFIG.apiUrl` near the top of the main JavaScript in `index.html` to your deployed endpoint, for example `https://your-api-domain.example/api/chat`. Add your production website domain to Firebase Authentication authorized domains, and make sure `ALLOWED_ORIGINS` contains that exact website origin.

## Limitations to know

- Chat history and personal memory are stored locally in this browser. They are not synced across devices yet.
- The attachment picker sends file metadata only; actual file contents need a separate secure upload flow.
- The studio pages are destination links only. Create the corresponding HTML files under `studios/` when ready.
- Image/audio generation requires separate integrations; the first working AI backend is text chat through OpenAI.
- This source was syntax-checked, but live Firebase sign-in and live OpenAI calls require your own backend secrets and were not testable from this generated file.
