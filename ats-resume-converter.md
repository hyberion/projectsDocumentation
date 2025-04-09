# 📝 ATS Resume Converter

A tool that accepts an uploaded resume (PDF or DOCX), parses the content, and outputs an ATS-friendly version in clean HTML, Markdown, or plain text. The goal is to help job seekers format resumes that won't get auto-rejected by Applicant Tracking Systems (ATS).

---

## 🔧 Tech Stack

### Core:

- **Node.js** or **Python** backend
- File upload (Express or Flask)
- HTML/CSS (optional frontend for drag-and-drop UI)

### File Parsing:

- PDF: [`pdf-parse`](https://www.npmjs.com/package/pdf-parse) (Node.js) or [`PyMuPDF`](https://pymupdf.readthedocs.io/) (Python)
- DOCX: [`mammoth`](https://www.npmjs.com/package/mammoth) (Node.js) or [`python-docx`](https://python-docx.readthedocs.io/)

### Formatting Output:

- Convert to:
  - Markdown
  - Plaintext
  - Optional: HTML-to-PDF using `puppeteer`, `html-pdf`, or `pdfkit`

### Optional Enhancements:
- AI integration with GPT (OpenAI API) for keyword suggestion, tone rewriting, and section summaries
- Section detection and scoring based on job description

---

## 📁 Project Structure

```bash
ats-resume-converter/
├── uploads/                # Resume files uploaded by user
├── output/                 # Converted output files (plain text, MD, etc.)
├── parser/                 # Resume parsing and cleanup logic
├── ai/                     # AI enrichment (summary, keyword insertion)
├── public/                 # Static frontend (optional)
├── routes/                 # File upload + processing endpoints
├── app.js / server.js      # Express or Flask app entry point
└── README.md
```

---

## ⚙️ Local Environment Setup

### Node.js Setup

```bash
git clone https://github.com/yourname/ats-resume-converter.git
cd ats-resume-converter
npm install
node app.js
```

### Python Setup

```bash
git clone https://github.com/yourname/ats-resume-converter.git
cd ats-resume-converter
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
python app.py
```

---

## ✨ Features (Planned / Staged)

| Feature                              | Status   |
|--------------------------------------|----------|
| Upload DOCX or PDF                   | ✅        |
| Parse and clean to plain format      | ✅        |
| Rebuild clean HTML/Markdown output  | ✅        |
| Flag common ATS issues               | ⏳        |
| AI-enhanced section summary          | ⏳        |
| Suggest keyword improvements         | ⏳        |
| Export clean version (TXT/MD/PDF)    | ⏳        |

---

## 📌 Future Ideas

- Visual report of "ATS issues found"
- Upload a job posting and match resume to job keywords
- Optional drag-and-drop block-based resume builder
- Save multiple versions (e.g. "Version for Google", "Version for NGO")

---

## 🧠 Why This Matters

> Most resumes never reach a human. If they’re not ATS-friendly, they’re discarded before being seen.

This tool can help:

- Non-technical users fix formatting without design skills
- Job seekers apply more confidently
- Recruiters help candidates improve submissions

---

## 📄 License

MIT or TBD

---

## 🤝 Contributions Welcome

Create an issue, submit a PR, or help expand the AI logic — this project is open for iteration.
