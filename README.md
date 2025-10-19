# AI Assistant Platform

A comprehensive AI-powered educational and interview preparation platform built with Gradio, featuring speech recognition, aptitude training, and intelligent interviewing capabilities powered by NVIDIA and Gemini APIs.

## Features

### 🎤 AI Interviewer
- **Interactive Interview Sessions**: Engage in realistic AI-powered interviews with customizable personas
- **Voice & Text Input**: Respond via typing or voice recording
- **Dynamic Conversation Flow**: AI adapts based on your responses
- **Multiple Difficulty Levels**: Choose from Easy, Medium, or Hard interview modes
- **Custom Personas**: Define specific interviewer personalities (HR, technical lead, etc.)

### 📚 Aptitude Tutor
- **Practice Questions**: Randomized aptitude questions from JSON database
- **Progress Tracking**: Monitor your accuracy and performance over time
- **Concept Explainer**: Get detailed explanations on any aptitude topic using NVIDIA API
- **AI Tutor Chat**: Interactive chatbot for aptitude-related queries
- **Immediate Feedback**: Instant validation and correct answer display

### 🛠️ Utilities
- **Speech-to-Text**: Transcribe uploaded or recorded audio files
- **Multi-format Support**: Compatible with various audio formats
- **Real-time Recording**: Record and transcribe voice directly in the browser

## Installation

### Prerequisites

```bash
Python 3.8+
Internet connection (for Google Speech Recognition and NVIDIA/Gemini APIs)
Microphone (for voice input features)
```

### Required Dependencies

```bash
pip install gradio
pip install SpeechRecognition
pip install pyttsx3
pip install sounddevice
pip install soundfile
pip install numpy
pip install asyncio
```

### Project Structure

```
project/
├── app.py                          # Main Gradio application
├── stt_service.py                  # Speech handler service
├── interviewer_logic_gemini.py     # Interview logic with NVIDIA LLM
├── summary_logic_gemini.py         # Concept explainer (optional)
├── data/
│   └── questions.json              # Aptitude questions database
└── README.md
```

## Quick Start

1. **Clone the repository:**
```bash
git clone <repository-url>
cd ai-assistant-platform
```

2. **Install dependencies:**
```bash
pip install -r requirements.txt
```

3. **Set up API keys:**
```bash
# Set your NVIDIA API key
export NVIDIA_API_KEY="your_nvidia_api_key_here"

# Set your Gemini API key (if applicable)
export GEMINI_API_KEY="your_gemini_api_key_here"
```

4. **Prepare questions database:**
Create `data/questions.json` with the following structure:
```json
[
  {
    "id": 1,
    "question": "What is 2 + 2?",
    "options": ["3", "4", "5", "6"],
    "correct_option_index": 1
  }
]
```

5. **Run the application:**
```bash
python app.py
```

6. **Access the interface:**
- Local: `http://localhost:7860`
- Public (if share=True): Check console for Gradio share link

## Usage Guide

### AI Interviewer Tab

1. **Configure Interview Settings:**
   - Enter a custom **Interviewer Persona** (e.g., "You are a senior software engineer conducting a technical interview")
   - Select **Difficulty Level** (Easy/Medium/Hard)

2. **Start Interview:**
   - Click **Start Interview** button
   - AI will greet you and ask the first question

3. **Respond:**
   - **Text Input**: Type response and click **Send Text** or press Enter
   - **Voice Input**: Click record button, speak, then click **🎤 Send Voice**

4. **Continue/End:**
   - Conversation continues until AI concludes interview
   - Click **Clear Interview** to reset and start over

### Aptitude Tutor Tab

#### Questions Practice
1. Click **Get New Question** to load a random question
2. Review options and select your answer (0-3)
3. Click **Submit Answer** for instant feedback
4. View correct answer and your current score

#### Progress Tracking
1. Navigate to **Progress** sub-tab
2. Click **Refresh Progress** to view:
   - Total questions attempted
   - Correct answers
   - Accuracy percentage

#### Concept Explainer
1. Go to **Concept Explainer** sub-tab
2. Enter a topic (e.g., "Probability", "Time and Distance")
3. Click **Explain Concept**
4. Receive detailed explanation from NVIDIA API

#### Tutor Chat
1. Open **Tutor Chat** sub-tab
2. Ask questions about aptitude concepts
3. Receive AI-powered responses
4. Build on conversation history

### Utilities Tab

#### Speech-to-Text (Uploaded Files)
1. Click **Upload Audio File**
2. Select audio file from your device
3. Click **Transcribe Uploaded**
4. View transcription result

#### Speech-to-Text (Recording)
1. Click microphone button to start recording
2. Speak clearly into microphone
3. Stop recording
4. Click **Transcribe Recorded**
5. View transcription result

## Configuration

### Custom Interview Personas

Modify the default persona in the Gradio interface or create presets:

```python
# Example personas
technical_persona = "You are a senior technical interviewer at a FAANG company. Focus on data structures, algorithms, and system design."

behavioral_persona = "You are an empathetic HR manager. Ask behavioral questions using the STAR method."

startup_persona = "You are a startup founder looking for a co-founder. Ask about entrepreneurial mindset and adaptability."
```

### Questions Database Format

`data/questions.json` structure:
```json
[
  {
    "id": 1,
    "question": "Question text here?",
    "options": ["Option A", "Option B", "Option C", "Option D"],
    "correct_option_index": 0
  }
]
```

### Speech Recognition Settings

Modify in `stt_service.py`:
```python
# Adjust recognition timeout
timeout = 5  # seconds

# Adjust ambient noise calibration
duration = 2  # seconds
```

## API Integration

### NVIDIA API (InterviewLogicNVIDIA)

Located in `interviewer_logic_gemini.py`:
- Handles interview conversation logic
- Generates contextual follow-up questions
- Determines when to conclude interview

### Concept Explainer API

Located in `summary_logic_gemini.py`:
- Provides detailed topic explanations
- Uses NVIDIA's language models
- Supports various aptitude subjects

### Google Speech Recognition

- Used for audio transcription
- Requires internet connection
- Free tier with usage limits

## Architecture

### Component Overview

```
┌─────────────────────────────────────────────────┐
│              Gradio Interface                    │
│  ┌──────────┬──────────────┬─────────────────┐  │
│  │Interviewer│ Aptitude    │   Utilities     │  │
│  │   Tab    │    Tutor     │      Tab        │  │
│  └──────────┴──────────────┴─────────────────┘  │
└─────────────────────────────────────────────────┘
                      │
        ┌─────────────┼─────────────┐
        │             │             │
   ┌────▼────┐  ┌────▼─────┐  ┌───▼────┐
   │Interview│  │ Aptitude │  │  STT   │
   │  Logic  │  │  Logic   │  │Handler │
   └────┬────┘  └────┬─────┘  └───┬────┘
        │            │            │
   ┌────▼────────────▼────────────▼────┐
   │        External APIs               │
   │  • NVIDIA LLM                      │
   │  • Gemini API                      │
   │  • Google Speech Recognition       │
   └────────────────────────────────────┘
```

### Session Management

- **Global State**: Tracks interview sessions and user progress
- **User Sessions**: Maintains chat history and scores per user
- **Async Operations**: Non-blocking API calls and audio processing

## Troubleshooting

### Common Issues

**"questions.json not found" error:**
```bash
# Create the data directory and add questions file
mkdir -p data
# Add your questions.json file to data/
```

**Microphone not working:**
```bash
# Check available microphones
python -c "import speech_recognition as sr; print(sr.Microphone.list_microphone_names())"

# Grant microphone permissions in browser
# Check system audio settings
```

**API connection errors:**
```bash
# Verify API keys are set
echo $NVIDIA_API_KEY
echo $GEMINI_API_KEY

# Check internet connection
ping google.com
```

**Audio transcription fails:**
```bash
# Install missing audio libraries
pip install pyaudio
pip install soundfile
pip install sounddevice
```

**Import errors:**
```bash
# Ensure all custom modules are in the same directory
ls -l interviewer_logic_gemini.py
ls -l stt_service.py
ls -l summary_logic_gemini.py
```

## Customization

### Custom CSS Styling

Modify the `custom_css` variable in `app.py`:
```python
custom_css = """
.gr-button {
    background: your-color !important;
}
"""
```

### Add New Question Categories

Extend `questions.json`:
```json
{
  "id": 100,
  "question": "Your question?",
  "options": ["A", "B", "C", "D"],
  "correct_option_index": 2,
  "category": "Logic",
  "difficulty": "Hard"
}
```

### Custom Interview Flow

Modify `InterviewLogicNVIDIA` class:
```python
class InterviewLogicNVIDIA:
    def __init__(self, persona: str, max_questions: int = 5):
        self.max_questions = max_questions
        # Add custom logic
```

## Performance Optimization

- **Async Operations**: All API calls use `asyncio` for non-blocking execution
- **Session Caching**: User data cached in memory for quick access
- **Lazy Loading**: Questions loaded on-demand
- **Thread Pooling**: Audio processing runs in separate threads

## Security Considerations

- **API Keys**: Never commit API keys to version control
- **User Data**: Session data stored in memory only (not persisted)
- **Audio Files**: Temporary audio files cleaned up automatically
- **Input Validation**: All user inputs validated before processing

## Contributing

Contributions are welcome! Please follow these guidelines:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit changes (`git commit -m 'Add AmazingFeature'`)
4. Push to branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## Known Limitations

- Speech recognition requires internet connection
- NVIDIA API rate limits may apply
- Voice recording quality depends on browser support
- Concurrent interview sessions not fully supported
- Questions database must be manually curated

## Future Enhancements

- [ ] Multi-user authentication system
- [ ] Persistent storage for user progress
- [ ] Advanced analytics dashboard
- [ ] Custom question upload interface
- [ ] Interview recording and playback
- [ ] Multi-language support
- [ ] Mobile-optimized interface
- [ ] Offline mode for basic features

## License

This project is provided for educational and development purposes.

## Acknowledgments

- **Gradio**: For the intuitive UI framework
- **NVIDIA**: For LLM API access
- **Google**: For Speech Recognition API
- **SpeechRecognition Library**: For audio transcription
- **pyttsx3**: For text-to-speech capabilities

## Support

For issues, questions, or suggestions:
- Open an issue on GitHub
- Check existing documentation
- Review troubleshooting section

## Changelog

### Version 1.0.0
- Initial release
- AI Interviewer with voice support
- Aptitude tutor with progress tracking
- Speech-to-text utilities
- NVIDIA and Gemini API integration