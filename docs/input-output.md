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
2025-02-19 09:35:04.691903+01:00,wa88822,0.999,0.707,/camera-1_screenshots/25-02-19/09-35-04.691903.jpg,"{'xmin': 483, 'ymin': 325, 'xmax': 540, 'ymax': 343}","[{'make': 'Peugeot', 'model': '207', 'score': 0.89}]",...
```

##### **JSONL Format**

Example:

```json
{
  "camera_id": "camera-1",
  "timestamp": "2025-02-19T09:35:04.691903Z",
  "filename": "09-35-04.691903.jpg",
  "results": [
    {
      "plate": "wa88822",
      "region": { "code": "pl", "score": 0.718 },
      "score": 0.999,
      "color": [{ "color": "black", "score": 0.926 }]
    }
  ],
  "source_url": "/user-data/videos/Sample-2-45s.mp4",
  "position_sec": "0.11"
}
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

### Example Webhook Payload:

```json
{
  "data": {
    "camera_id": "camera-1",
    "filename": "camera-1_screenshots/21-10-27/06-20-55.161444.jpg",
    "results": [
      {
        "plate": "slz9043m",
        "score": 0.906,
        "region": { "code": "it", "score": 0.476 },
        "color": [{ "color": "white", "score": 0.889 }]
      }
    ],
    "timestamp": "2021-10-27T06:20:55.161444Z"
  },
  "hook": {
    "event": "recognition",
    "filename": "camera-1_screenshots/21-10-27/06-20-55.161444.jpg",
    "id": "camera-1",
    "target": "https://webhook.site/example"
  }
}
```

![Webhook example](/files/sample-webook-response.png "Webhook example")
