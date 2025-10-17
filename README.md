# Speech Handler

A Python library for handling speech-to-text and text-to-speech operations with async support and continuous conversation capabilities.

## Features

- **Text-to-Speech (TTS)**: Convert text to speech with customizable voice settings
- **Speech-to-Text (STT)**: Convert speech to text using Google's speech recognition
- **Async Support**: Non-blocking operations using Python's asyncio
- **Continuous Conversation**: Built-in support for ongoing voice interactions
- **Thread-Safe**: Queue-based TTS processing for safe concurrent operations
- **Audio Playback**: Direct audio playback with interruption support
- **File Export**: Save generated speech to WAV files

## Requirements

```bash
pip install playsound sounddevice soundfile numpy SpeechRecognition pyttsx3
```

### System Requirements

- Python 3.10.11(mandatory requirement)
- Working microphone for speech recognition
- Audio output device for speech playback
- Internet connection (for Google Speech Recognition API)

## Quick Start

```python
import asyncio
from speech_handler import SpeechHandler

async def main():
    handler = SpeechHandler()
    
    try:
        # Text to Speech
        await handler.text_to_speech("Hello, welcome to Speech Handler!")
        
        # Speech to Text
        print("Please speak...")
        text = await handler.speech_to_text(timeout=5)
        print(f"You said: {text}")
        
    finally:
        handler.shutdown()

if __name__ == "__main__":
    asyncio.run(main())
```

## Usage Examples

### Basic Text-to-Speech

```python
# Async TTS (returns audio buffer)
audio_buffer = await handler.text_to_speech("Hello world")

# Fire-and-forget TTS (non-blocking)
handler.speak("This will play in the background")
```

### Speech Recognition

```python
# Listen for speech with timeout
recognized_text = await handler.speech_to_text(timeout=5)
if recognized_text:
    print(f"Recognized: {recognized_text}")
```

### Continuous Conversation

```python
async def my_callback(recognized_text: str) -> bool:
    """Handle recognized speech and return False to stop conversation"""
    if 'exit' in recognized_text:
        handler.speak("Goodbye!")
        return False
    
    response = f"You said: {recognized_text}"
    handler.speak(response)
    return True

# Start continuous listening
await handler.continuous_conversation(callback=my_callback)
```

### Save Speech to File

```python
await handler.save_speech_to_file("Hello world", "output.wav")
```

### Customize Voice Settings

```python
# View available voices
handler.get_voice_info()

# Voice settings are configured in setup_tts()
# Modify rate (50-300), volume (0.0-1.0), and voice selection
```

## API Reference

### SpeechHandler Class

#### Initialization
```python
handler = SpeechHandler()
```
Automatically calibrates microphone and starts TTS worker thread.

#### Methods

**`async text_to_speech(text: str) -> io.BytesIO`**
- Converts text to speech and returns audio buffer
- Non-blocking async operation

**`speak(text: str)`**
- Fire-and-forget speech output
- Non-blocking, uses background thread

**`async speech_to_text(timeout: int = 5) -> Optional[str]`**
- Listens for speech and converts to text
- Returns recognized text or None

**`async continuous_conversation(callback: Callable)`**
- Runs continuous speech recognition loop
- Callback receives recognized text and returns bool to continue/stop

**`async save_speech_to_file(text: str, filename: str = "output.wav")`**
- Saves generated speech to WAV file

**`play_audio(temp_file)`**
- Plays audio file using sounddevice
- Returns stream object for interruption control

**`calibrate_microphone()`**
- Adjusts for ambient noise
- Called automatically during initialization

**`get_voice_info()`**
- Displays available TTS voices

**`shutdown()`**
- Clean shutdown of speech handler
- Should be called before program exit

## Configuration

### TTS Settings

Modify in `setup_tts()` method:
```python
self.tts_engine.setProperty('rate', 150)      # Speech rate (50-300)
self.tts_engine.setProperty('volume', 0.8)    # Volume (0.0-1.0)
self.tts_engine.setProperty('voice', voices[1].id)  # Voice selection
```

### Microphone Calibration

```python
# Adjust calibration duration (default: 2 seconds)
self.recognizer.adjust_for_ambient_noise(source, duration=2)
```

## Error Handling

The library includes built-in error handling for common issues:
- Microphone not available
- Speech recognition timeout
- Network errors (Google API)
- TTS engine failures
- Audio playback errors

Errors are logged to console with descriptive messages.

## Architecture

- **Thread-Safe TTS**: Queue-based processing prevents threading conflicts
- **Async Operations**: Uses `asyncio` and thread pools for non-blocking I/O
- **Modular Design**: Separate methods for each operation
- **Resource Management**: Automatic cleanup of temporary files and threads

## Limitations

- Requires internet connection for speech recognition
- Google Speech Recognition API may have usage limits
- Voice availability depends on system TTS engine
- Microphone access required for STT features

## Troubleshooting

**No microphone detected:**
```python
# List available microphones
import speech_recognition as sr
print(sr.Microphone.list_microphone_names())
```

**Speech recognition not working:**
- Check internet connection
- Ensure microphone permissions are granted
- Verify microphone is not muted
- Try recalibrating: `handler.calibrate_microphone()`

**Audio playback issues:**
- Verify audio output device is working
- Check system audio settings
- Ensure no other application is blocking audio

## Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

## License

This project is provided as-is for educational and development purposes.

## Acknowledgments

- Built with [pyttsx3](https://github.com/nateshmbhat/pyttsx3) for TTS
- Uses [SpeechRecognition](https://github.com/Uberi/speech_recognition) for STT
- Audio processing with [sounddevice](https://python-sounddevice.readthedocs.io/) and [soundfile](https://github.com/bastibe/python-soundfile)
