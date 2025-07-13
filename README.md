# Haptic-Feel (Vibe for F1 Haptic Trailer) -> Soon for Android 11+
This project generates haptic feedback patterns for Android devices by analyzing audio and visual features for movie trailers. The pipeline extracts features from video files, trains a machine learning model, and outputs haptic events in a JSON format compatible with Android's vibration API.

# Features (Python ML)

🎬 Video processing with OpenCV

🔊 Audio feature extraction using Librosa

🤖 Random Forest classifier for haptic event prediction

📊 Visualization of results and feature importance

📱 JSON output compatible with Android's Vibrator API

# Usage (Python ML)

```
from haptic_generator import main_pipeline

# Generate haptic pattern from video
success = main_pipeline(
    video_path="trailer.mp4",
    output_json_path="haptic_output.json",
    frame_skip=5
)

if success:
    print("Haptic pattern generated successfully!")
```

# Pipeline Steps
1) Audio Extraction: Convert video to WAV audio

2) Audio Sampling: MFCC, chroma, spectral centroid, RMS energy

3) Video Sampling: Motion intensity, color variance, brightness, edge density

3) Feature Combination: Temporal alignment of audio/video features

4) Model Training: Random Forest classifier with automatic labeling

5) Haptic Prediction: Generate vibration events with timing and intensity

6) JSON Export: Android-compatible output format

# Sample Output Structure

```
{
  "version": "1.0",
  "metadata": {
    "source_video": "trailer.mp4",
    "duration": 142.3,
    "model_accuracy": 0.87,
    "total_events": 203
  },
  "pattern": {
    "events": [
      {
        "time": 12.45,
        "type": "vibrate",
        "intensity": 180,
        "duration": 100
      },
      {
        "time": 18.72,
        "type": "vibrate",
        "intensity": 220,
        "duration": 50
      }
    ],
    "totalDuration": 145.8
  }
}
```

# Customization
Modify these aspects in the code:

```
# In create_training_labels():
# Adjust haptic scoring thresholds
if combined_features[i, -8] > np.percentile(combined_features[:, -8], 80):
    haptic_score += 2

# In predict_haptic_events():
# Modify intensity mapping
intensity = int(min(255, max(50, prob[pred] * 300)))  # Increase sensitivity

# Add new video features in extract_video_features():
features['new_feature'] = []  # Implement your feature
```

# Features (Android)

🎥 Video playback with ExoPlayer

📳 Synchronized haptic feedback

⏯️ Playback controls with haptic sync

📊 Real-time playback status

🔄 Seek synchronization between video and haptics

# Key Components 
HapticActivity.kt: Main player activity

HapticManager.kt: Haptic feedback controller

HapticPattern.json: Generated pattern file (from ML pipeline)

# Setup Instructions
Add generated haptic_pattern.json to app/src/main/assets

Add video file to app/src/main/res/raw

Update video URI in loadVideo():

# Synchronization Logic
The player maintains perfect sync between video and haptics by:

Tracking video position with exoPlayer.currentPosition

Calculating event timing relative to video start

Adjusting haptic playback during seeks:

```
override fun onPositionDiscontinuity(reason: Int) {
    if (reason == Player.DISCONTINUITY_REASON_SEEK) {
        hapticManager.seekTo(newPosition.positionMs)
    }
}
```

Customization Options
Add new haptic types:

```
"pulse" -> {
    // Create custom pulse pattern
    val pattern = longArrayOf(0, 50, 50, 50, event.duration.toLong())
    val amplitudes = intArrayOf(0, event.intensity, 0, event.intensity)
    val vibrationEffect = VibrationEffect.createWaveform(pattern, amplitudes, -1)
}
```
Adjust intensity mapping:

```
// Boost intensity sensitivity
val amplitude = (event.intensity * 1.5).toInt().coerceIn(1, 255)
```
