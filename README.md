# Kotoba TTS Android Package

https://github.com/kotoba-tech/kotoba-tts-android/packages

## Features

- **Streaming TTS Generation**: Real-time audio generation with chunk callbacks
- **Multi-language Support**: Japanese, English, Chinese, and Korean
- **High Quality**: 24kHz audio output

## Setup

1. Add dependency settings to `settings.gradle`:
   ```gradle
   dependencyResolutionManagement {
       repositories {
           maven {
               url = uri("https://maven.pkg.github.com/kotoba-tech/kotoba-tts-android")
               credentials {
                   username = settings.providers.gradleProperty("gpr.user").orNull ?: System.getenv("GITHUB_ACTOR")
                   password = settings.providers.gradleProperty("gpr.key").orNull ?: System.getenv("GITHUB_TOKEN")
               }
           }
       }
   }
   ```

2. Create a GitHub Personal Access Token with `read:packages` permission
3. Add your GitHub credentials to `local.properties`:
   ```
   gpr.user=YOUR_GITHUB_USERNAME
   gpr.key=YOUR_GITHUB_PERSONAL_ACCESS_TOKEN
   ```
4. Add package depdendency to `app/build.gradle`:
   ```gradle
   dependencies {
        implementation 'com.kotoba:kotoba-tts-android:1.0.0'
    }
   ```
5. Build and run the app

## Quick Start

```kotlin
import com.kotoba.tts.KotobaTTS
import com.kotoba.tts.AudioChunkCallback
import kotlinx.coroutines.launch

class TTSExample {
    suspend fun generateSpeech() {
        val callback = object : AudioChunkCallback {
            override fun onAudioChunk(
                audioData: FloatArray,      //  Float array containing audio samples (PCM format)
                sampleRate: Int,            //  Sample rate in Hz (e.g., 24000)
                model1Duration: Double,     //  Time taken by model1 transformer (seconds)
                model2Duration: Double,     //  Time taken by model2 transformer (seconds)
                decoderDuration: Double,    //  Time taken by decoder (seconds)
                stepDuration: Double        //  Total time for this generation step (seconds)
            ) {
                // Process audio chunk
                playAudio(audioData, sampleRate)
            }
        }
        
        val result = KotobaTTS.generateTTSStreaming(
            model1Path = "/path/to/model1.gguf",
            model2Path = "/path/to/model2.gguf",
            decoderPath = "/path/to/decoder.gguf",
            text = "おはようございます。今日はよろしくお願いします。",
            language = "ja",    // Default to Japanese
            callback = callback
        )
    }
}
```