
# Student Report Delivery App (Prototype)

This is a local Flask app that:
- Accepts an uploaded CSV/XLSX with student marks (7 subjects).
- Generates one bilingual (English + Urdu) PDF per student.
- Sends email via SendGrid (attach PDF) if configured.
- Sends plain-text WhatsApp messages via Twilio (sandbox or production) if configured.
- Produces a delivery CSV and a ZIP of generated PDFs.

Quick features:
- Flexible column detection (tries to map common column names).
- Saves PDFs to `output/pdfs/` and delivery reports to `output/`.
- Configurable via environment variables or a `.env` file.

Required files in input (expected headers — case-insensitive):
- Student ID (e.g., "Student ID")
- Student Name (e.g., "Student Name")
- English Obtained/100 (or English)
- Urdu Obtained/100 (or Urdu)
- Maths / Math Obtained/100 (or Maths)
- Science Obtained/100
- Computer Obtained/100
- History Obtained/100
- Geography Obtained/100
- Total Obtained/700 (optional — app will compute if missing)
- Percentage (optional — app will compute if missing)
- Phone no (optional — for WhatsApp)
- Email (optional — for email)

Files included:
- app.py — main Flask app
- requirements.txt — Python deps
- templates/index.html — upload page
- templates/report.html — per-student PDF HTML template
- templates/result.html — results & download links
- static/css/style.css — template styles
- sample_students.csv — a sample CSV to test
- output/ (created at runtime) — PDFs, zip, CSV

Important external dependency:
- wkhtmltopdf (used by pdfkit) — must be installed on your machine. See install instructions below.

Setup (Linux / macOS / Windows WSL recommended)
1. Install wkhtmltopdf
   - Ubuntu/Debian:
     sudo apt-get install -y wkhtmltopdf
   - macOS (Homebrew):
     brew install wkhtmltopdf
   - Windows:
     Download installer from https://wkhtmltopdf.org/downloads.html and add to PATH.

2. Create a Python virtualenv and install requirements:
   python3 -m venv venv
   source venv/bin/activate   # (Windows: venv\Scripts\activate)
   pip install -r requirements.txt

3. Create a `.env` file (or export environment variables). Example `.env`:
   FLASK_ENV=development
   FLASK_APP=app.py
   WKHTMLTOPDF_PATH=/usr/local/bin/wkhtmltopdf   # adjust path if needed
   SENDGRID_API_KEY=your_sendgrid_api_key_here
   FROM_EMAIL=school@example.com
   TWILIO_ACCOUNT_SID=your_twilio_sid
   TWILIO_AUTH_TOKEN=your_twilio_token
   TWILIO_WHATSAPP_FROM=whatsapp:+1415xxxxxxx   # Twilio sandbox or number
   SCHOOL_NAME=My School Name
   SCHOOL_LOGO_URL=     # Optional public URL to logo
   DELETE_AFTER_DAYS=7  # optional cleanup

4. Run the app:
   flask run --host=0.0.0.0 --port=5000
   Open http://localhost:5000 in a browser (or on phone using laptop's IP on the same network).

Usage
- Upload the teacher's Excel or CSV file. The app will parse and process immediately, creating PDFs and attempting delivery where configured.
- After processing you'll get a summary, download link for ZIP of all PDFs, and delivery CSV.

Testing email/WhatsApp
- Email: Provide SENDGRID_API_KEY and FROM_EMAIL (verify sender in SendGrid).
- WhatsApp: Use Twilio WhatsApp Sandbox for testing:
  - In Twilio Console -> Messaging -> Try WhatsApp -> enable sandbox and follow join instructions (send join code from test phone).
  - Use provided sandbox number as TWILIO_WHATSAPP_FROM (prefixed with "whatsapp:").
  - Only phones that joined the sandbox can receive messages.

Notes / Next steps
- I set English on top and Urdu below in each PDF. Fonts for Urdu: if you want nicer Urdu typography, put a Urdu TTF in `static/fonts/` and update CSS.
- The app skips sending when contact info is missing and logs status in the delivery CSV.
- For production WhatsApp messaging you must register a WhatsApp Business account and get message templates approved by Meta.

If you want, I can:
- Help you verify SendGrid sender and test-send real emails.
- Walk you through Twilio sandbox or production WhatsApp onboarding.
- Package the app into a self-contained installer or a Docker image for easier delivery.

Next: run the app locally, upload your provided CSV and test. Tell me any issues and I will iterate quickly.
