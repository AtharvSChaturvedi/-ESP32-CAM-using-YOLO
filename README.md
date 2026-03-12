# ESP32-CAM Edge Impulse Inferencing Demo

Real-time on-device ML inference using an ESP32-CAM and a trained [Edge Impulse](https://edgeimpulse.com/) model. Captures camera frames, preprocesses them, and runs object detection, image classification, or anomaly detection — all without a cloud connection.

---

## Hardware Requirements

- ESP32-CAM board — **AI Thinker** (default) or **ESP-EYE**
- USB-to-Serial adapter (for flashing)
- PSRAM-enabled ESP32 module (required for camera frame buffering)

---

## Software Requirements

- [Arduino IDE](https://www.arduino.cc/en/software) with **ESP32 Arduino Core 2.0.4**
- Your exported Edge Impulse Arduino library (e.g. `esp32cam_detection_inferencing.zip`)
- `esp_camera` library (bundled with ESP32 Arduino Core)

---

## Setup

### 1. Train & Export Your Model
1. Create and train a project at [edgeimpulse.com](https://edgeimpulse.com/)
2. Go to **Deployment → Arduino Library** and download the `.zip`
3. In Arduino IDE: **Sketch → Include Library → Add .ZIP Library**

### 2. Select Your Camera Model
In the sketch, uncomment the line matching your board:

```cpp
// #define CAMERA_MODEL_ESP_EYE
#define CAMERA_MODEL_AI_THINKER  // ← default
```

### 3. Flash the Sketch
1. Connect your ESP32-CAM via USB-to-Serial adapter
2. Select the correct board and port in Arduino IDE
3. Upload the sketch

---

## How It Works

```
Camera Capture (JPEG, 320×240)
        ↓
Convert to RGB888
        ↓
Crop & Resize to Model Input Size
        ↓
Run Edge Impulse Classifier
        ↓
Print Results over Serial
```

1. **Camera init** — Configures the OV2640 sensor at QVGA (320×240) resolution
2. **Frame capture** — Grabs a JPEG frame and decodes it to RGB888
3. **Preprocessing** — Crops and interpolates the image to the model's expected input dimensions
4. **Inference** — Runs the Edge Impulse impulse on-device
5. **Output** — Prints predictions over Serial (115200 baud)

---

## Serial Output Examples

**Object Detection:**
```
Predictions (DSP: 12 ms., Classification: 87 ms., Anomaly: 0 ms.):
Object detection bounding boxes:
  cat (0.92340) [ x: 45, y: 30, width: 120, height: 95 ]
```

**Classification:**
```
Predictions:
  cat: 0.91245
  dog: 0.06123
  background: 0.02632
```

**Anomaly Detection:**
```
Anomaly prediction: 0.732
```

---

## Configuration

| Constant | Default | Description |
|---|---|---|
| `EI_CAMERA_RAW_FRAME_BUFFER_COLS` | `320` | Raw capture width (px) |
| `EI_CAMERA_RAW_FRAME_BUFFER_ROWS` | `240` | Raw capture height (px) |
| `debug_nn` | `false` | Set `true` to log DSP features |
| `jpeg_quality` | `12` | JPEG quality (0–63, lower = better) |

---

## Troubleshooting

| Issue | Fix |
|---|---|
| `Camera init failed` | Check wiring; ensure PSRAM is enabled in board settings |
| `Failed to allocate snapshot buffer` | Free up heap; reduce JPEG quality or frame size |
| `Invalid model for current sensor` | Ensure your Edge Impulse model was trained for a **camera** sensor |
| Garbled Serial output | Confirm baud rate is set to **115200** in Serial Monitor |

---

## License

MIT License — © 2022 EdgeImpulse Inc.  
See source file header for full license text.
