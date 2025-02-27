# Input-Output Documentation - LPR AI

## Input

### File Upload API

Uploads a video to the web service for processing by AI.

#### Example Requests:

##### cURL:

```sh
curl -F "upload=@/path/to/video.mp4" http://localhost:8081
```

##### Python:

```python
import requests

url = "http://localhost:8081"
file_path = "path/to/video.mp4"

with open(file_path, "rb") as file:
    response = requests.post(url, files={"upload": file})

if response.status_code == 200:
    print("File successfully uploaded.")
    print("Server response:", response.json())
else:
    print("Error occurred during file upload.")
    print("Status code:", response.status_code)
    print("Server response:", response.text)
```

#### Example API Response:

```json
{
  "status_code": 200,
  "filename": "video.mp4",
  "start_time": "2025-02-27T09:27:33.915556",
  "video_seconds": 108000,
  "video_usage": 248,
  "config_changes": {}
}
```

---

## Output

### Downloading Result Files

#### Configurable File Types:

##### **CSV Format**

Example:

```csv
timestamp,plate,score,dscore,file,box,model_make,color,vehicle,region,orientation,candidates,source_url,position_sec,direction
2025-02-19 09:35:04.691903+01:00,wa88822,0.999,0.707,/camera-1_screenshots/25-02-19/09-35-04.691903.jpg,"{'xmin': 483, 'ymin': 325, 'xmax': 540, 'ymax': 343}","[{'make': 'Peugeot', 'model': '207', 'score': 0.89}, {'make': 'generic', 'model': 'Unknown', 'score': 0.002}, {'make': 'generic', 'model': 'Unknown', 'score': 0.001}]","[{'color': 'black', 'score': 0.926}, {'color': 'silver', 'score': 0.021}, {'color': 'blue', 'score': 0.01}]","{'score': 0.868, 'type': 'Sedan', 'box': {'xmin': 436, 'ymin': 184, 'xmax': 676, 'ymax': 357}}","{'code': 'pl', 'score': 0.718}","[{'orientation': 'Front', 'score': 0.931}, {'orientation': 'Rear', 'score': 0.04}, {'orientation': 'Unknown', 'score': 0.028}]","[{'score': 0.999, 'plate': 'wa88822'}, {'score': 0.864, 'plate': 'wa8b822'}, {'score': 0.728, 'plate': 'wa8bb22'}, {'score': 0.728, 'plate': 'wab8b22'}, {'score': 0.728, 'plate': 'wabb822'}, {'score': 0.593, 'plate': 'wabbb22'}]",/user-data/videos/Sample-2-45s.mp4,0.11,180
```

##### **JSONL Format**

Example:

```jsonl
{"camera_id": "camera-1", "timestamp": "2025-02-19T09:35:04.691903Z", "filename": "09-35-04.691903.jpg", "results": [{"box": {"xmin": 483, "ymin": 325, "xmax": 540, "ymax": 343}, "plate": "wa88822", "region": {"code": "pl", "score": 0.718}, "score": 0.999, "candidates": [{"score": 0.999, "plate": "wa88822"}, {"score": 0.864, "plate": "wa8b822"}, {"score": 0.728, "plate": "wa8bb22"}, {"score": 0.728, "plate": "wab8b22"}, {"score": 0.728, "plate": "wabb822"}, {"score": 0.593, "plate": "wabbb22"}], "dscore": 0.707, "vehicle": {"score": 0.868, "type": "Sedan", "box": {"xmin": 436, "ymin": 184, "xmax": 676, "ymax": 357}}, "model_make": [{"make": "Peugeot", "model": "207", "score": 0.89}, {"make": "generic", "model": "Unknown", "score": 0.002}, {"make": "generic", "model": "Unknown", "score": 0.001}], "color": [{"color": "black", "score": 0.926}, {"color": "silver", "score": 0.021}, {"color": "blue", "score": 0.01}], "orientation": [{"orientation": "Front", "score": 0.931}, {"orientation": "Rear", "score": 0.04}, {"orientation": "Unknown", "score": 0.028}], "direction": 180, "speed": null, "speed_score": 0.0, "source_url": "\/user-data\/videos\/Sample-2-45s.mp4", "position_sec": "0.11", "user_data": ""}], "timestamp_local": "2025-02-19 09:35:04.691903+0100", "timestamp_camera": null}
```

##### **Image**

![Image of detected vehicle](/files/sample.jpg "Detected vehicle")

##### **Video**

A video clip of the detected vehicle.  
Configurable options:

- Time before/after detection
- FPS
- Codec
- Data overlays

[[Watch the video]](/files/sample.mp4)

## Webhook

A webhook sends real-time detection information encoded in `multipart/form-data`.

### Configuration Options:

- **Image:** Original image, car, license plate, or none.
- **Video:** File, URL, or none (sent in a separate POST request).
- **Header**

### Example Webhook Payload:

```json
{
  "data": {
    "camera_id": "camera-1",
    "filename": "/camera-1_screenshots/25-02-19/09-35-04.691903.jpg",
    "results": [
      {
        "box": {
          "xmin": 483,
          "ymin": 325,
          "xmax": 540,
          "ymax": 343
        },
        "candidates": [
          {"plate": "wa88822", "score": 0.999},
          {"plate": "wa8b822", "score": 0.864},
          {"plate": "wa8bb22", "score": 0.728}
        ],
        "color": [
          {"color": "black", "score": 0.926},
          {"color": "silver", "score": 0.021},
          {"color": "blue", "score": 0.01}
        ],
        "direction": 180,
        "dscore": 0.707,
        "model_make": [
          {"make": "Peugeot", "model": "207", "score": 0.89},
          {"make": "generic", "model": "Unknown", "score": 0.002}
        ],
        "orientation": [
          {"orientation": "Front", "score": 0.931},
          {"orientation": "Rear", "score": 0.04}
        ],
        "plate": "wa88822",
        "position_sec": "0.11",
        "region": {
          "code": "pl",
          "score": 0.718
        },
        "score": 0.999,
        "source_url": "/user-data/videos/Sample-2-45s.mp4",
        "vehicle": {
          "score": 0.868,
          "type": "Sedan",
          "box": {
            "xmin": 436,
            "ymin": 184,
            "xmax": 676,
            "ymax": 357
          }
        }
      }
    ],
    "timestamp": "2025-02-19T09:35:04.691903+01:00",
    "timestamp_local": "2025-02-19 09:35:04.691903+01:00",
    "timestamp_camera": null
  },
  "hook": {
    "event": "recognition",
    "filename": "/camera-1_screenshots/25-02-19/09-35-04.691903.jpg",
    "id": "camera-1",
    "target": "https://webhook.site/example"
  }
}
```

![Webhook example](/files/sample-webook-response.png "Webhook example")
