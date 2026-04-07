# Medi-Bridge 🏥

An AI-powered medical agentic mobile app that helps patients understand their prescriptions, lab reports, and health conditions — in plain language.

---

## Features

- **Prescription Scanner** — Photograph a prescription; Gemini 2.5 Flash extracts all medicines, dosages, and instructions as structured JSON
- **AI Voice Chat** — Talk to Medi AI (powered by Claude) about your medicines, lab results, or health concerns; responds in Hindi or English
- **Lab Report Analysis** — Upload a blood test PDF or photo; Gemini reads every test result and explains it in plain language with normal/watch/high/low status
- **Medications Manager** — Track active, paused, and completed medicines; toggle, reorder, or remove with one tap
- **Nutrition Expert** — Get a 7-day meal plan tailored to your condition and current medicines
- **Prescription Context** — After scanning, tap "Ask AI" and the assistant automatically explains every medicine warmly without you having to type anything

---

## Tech Stack

### Mobile
| | |
|---|---|
| Framework | React Native + Expo SDK 54 |
| Language | TypeScript |
| State | Zustand |
| Navigation | React Navigation (Stack + Bottom Tabs) |
| HTTP | Axios |
| Audio | expo-av |
| Camera | expo-image-picker |

### Backend
| | |
|---|---|
| Framework | FastAPI (Python 3.9) |
| AI — Chat | Anthropic Claude (`claude-sonnet-4-6`) |
| AI — OCR | Google Gemini 2.5 Flash |
| Orchestration | LangGraph |
| Vector DB | ChromaDB |
| Cache / Sessions | Redis |
| Database | PostgreSQL |
| Speech-to-Text | OpenAI Whisper |

### Infrastructure
| | |
|---|---|
| Containers | Docker Compose |
| Services | Postgres 15, Redis 7, ChromaDB |

---

## Project Structure

```
medi-bridge/
├── backend/
│   ├── agents/
│   │   ├── asr_agent.py               # Whisper speech-to-text
│   │   ├── enhanced_reasoning_agent.py # Claude with prescription awareness
│   │   ├── orchestrator.py            # LangGraph pipeline
│   │   ├── rag_agent.py               # ChromaDB retrieval
│   │   └── tts_agent.py               # Text-to-speech
│   ├── routers/
│   │   ├── voice.py                   # Chat endpoints
│   │   ├── prescriptions.py           # Gemini OCR scan
│   │   ├── reports.py                 # Lab report analysis
│   │   ├── nutrition.py               # Meal plans
│   │   └── orders.py                  # Medicine ordering
│   ├── services/
│   │   ├── claude.py                  # Anthropic client
│   │   ├── gemini_service.py          # Gemini prescription OCR
│   │   └── lab_analysis_service.py    # Gemini lab report OCR
│   ├── models/                        # Pydantic schemas
│   ├── db/                            # Postgres + Redis clients
│   └── main.py
├── mobile/
│   └── src/
│       ├── screens/
│       │   ├── VoiceChatScreen.tsx    # AI chat with typing indicator
│       │   ├── ScanScreen.tsx         # Camera + prescription scan
│       │   ├── LabReportScreen.tsx    # Lab report upload + results
│       │   ├── MedicationsScreen.tsx  # Medication tracker
│       │   ├── NutritionScreen.tsx    # Meal plan
│       │   └── HomeScreen.tsx
│       ├── components/                # Button, Pill, MedicineCard, ChatBubble, LabResultRow, BottomNav
│       ├── hooks/                     # useAgent, useVoice
│       ├── services/api.ts            # Axios API client
│       ├── store/useAppStore.ts       # Zustand store
│       └── theme/tokens.ts            # Design tokens
├── rag/
│   ├── ingest/                        # Drug DB, nutrition corpus, clinical guidelines
│   └── retriever/chroma_retriever.py
└── infra/
    └── docker-compose.yml
```

---

## Getting Started

### Prerequisites
- Python 3.9+
- Node.js 18+
- Docker Desktop
- Expo Go app on your phone

### 1. Clone the repo
```bash
git clone https://github.com/atulsingh-pm-ai/MEDI-BRIDGE.git
cd MEDI-BRIDGE
```

### 2. Start infrastructure
```bash
cd infra
docker-compose up -d
```

### 3. Backend setup
```bash
cd backend
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt

cp .env.example .env
# Fill in your API keys in .env
```

### 4. Start backend
```bash
PYTHONPATH=.:../rag venv/bin/uvicorn main:app --host 0.0.0.0 --port 8001 --reload
```

### 5. Mobile setup
```bash
cd mobile
npm install
npx expo start --host lan
```
Scan the QR code with Expo Go on your phone.

---

## Environment Variables

Create `backend/.env` from `backend/.env.example`:

```env
ANTHROPIC_API_KEY=your_anthropic_key
GEMINI_API_KEY=your_gemini_key
POSTGRES_URL=postgresql+asyncpg://postgres:postgres@localhost:5432/medibridge
REDIS_URL=redis://localhost:6379
CHROMA_HOST=localhost
CHROMA_PORT=8000
```

Get your keys:
- Anthropic: [console.anthropic.com](https://console.anthropic.com)
- Gemini: [aistudio.google.com](https://aistudio.google.com)

---

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/voice/text` | Send text message to AI |
| POST | `/voice/audio` | Send audio recording |
| POST | `/prescriptions/scan` | Scan prescription image |
| POST | `/reports/analyse` | Analyse lab report (PDF or image) |
| POST | `/reports/explain-test` | Explain a single test result |
| POST | `/nutrition/chat` | Nutrition assistant chat |
| POST | `/nutrition/meal-plan` | Generate 7-day meal plan |
| GET | `/health` | Health check |

Interactive docs: `http://localhost:8001/docs`

---

## How It Works

```
User scans prescription
        ↓
Gemini 2.5 Flash reads image → structured JSON (medicines, doctor, date)
        ↓
User taps "Ask AI about these"
        ↓
VoiceChat opens → auto-sends prescription context to backend
        ↓
LangGraph pipeline:
  RAG Agent → retrieves drug info from ChromaDB
  Enhanced Reasoning Agent → Claude generates warm, plain-language explanation
  TTS Agent → optional audio response
        ↓
Patient understands their prescription 💊
```

---

## License

MIT
