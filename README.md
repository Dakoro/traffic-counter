# Traffic Counter

A real-time vehicle traffic counting system using YOLOv8 and SORT tracking algorithm. Detects, tracks, and counts vehicles crossing predefined lanes in traffic camera footage.

<video src="https://drive.google.com/file/d/1reUv647BtclrbN46eQ7cgPjcc61ydpcv/view?usp=sharing" width="500" height="300" controls></video>

## Features

- **Real-time Vehicle Detection**: Uses YOLOv8-Large model to detect cars, trucks, buses, and motorbikes
- **Multi-Object Tracking**: Implements SORT (Simple Online and Realtime Tracker) algorithm with Kalman filtering
- **Lane-based Counting**: Monitors two virtual detection lines for bidirectional traffic analysis
- **Live Visualization**: Displays bounding boxes, tracking IDs, and real-time statistics
- **Noise Reduction**: Applies masking to exclude irrelevant image regions
- **Video Export**: Saves annotated results with overlay graphics

## Demo

The system processes traffic camera footage and generates an annotated video (`result.mp4`) showing:
- Bounding boxes around detected vehicles
- Unique tracking IDs for each vehicle
- Detection lines that change color when vehicles cross
- Lane-wise and total vehicle counts

## Prerequisites

- Python 3.12 or higher
- Traffic camera video footage (or use provided sample)

## Installation

1. Clone the repository:
```bash
git clone https://github.com/yourusername/traffic-counter.git
cd traffic-counter
```

2. Create a virtual environment and activate it:
```bash
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
```

3. Install dependencies using pip:
```bash
pip install cvzone>=1.6.1 filterpy>=1.4.5 numpy>=2.3.4 opencv-python>=4.11.0.86 scikit-image>=0.25.2 ultralytics>=8.3.220
```

Or if using `uv` package manager:
```bash
uv sync
```

## Project Structure

```
traffic-counter/
    main.py                 # Main application entry point
    pyproject.toml          # Project configuration and dependencies
    assets/
        traffic_cam.mp4     # Input traffic camera video
        graphics.png        # Total count display overlay
        graphics1.png       # Lane count display overlay
        mask.png           # Noise reduction mask
    utils/
        sort.py            # SORT tracking algorithm implementation
    path/to/
        yolov8l.pt         # YOLOv8 Large pre-trained model
    result.mp4             # Output video (generated)
```

## Usage

Run the traffic counter:

```bash
python main.py
```

The application will:
1. Load the YOLOv8 model and input video
2. Process each frame with object detection and tracking
3. Display real-time results in an OpenCV window
4. Save the annotated video to `result.mp4`

Press `q` to quit the live display.

## How It Works

### Processing Pipeline

```
Video Input � Frame Reading � Mask Application � YOLOv8 Detection
    �
Vehicle Filtering � SORT Tracking � Line Crossing Detection
    �
Visualization & Annotation � Overlay Graphics � Video Output
```

### Key Components

1. **Object Detection (YOLOv8)**
   - Pre-trained YOLOv8-Large model on COCO dataset
   - Filters detections for vehicle classes only (car, truck, bus, motorbike)

2. **Tracking (SORT)**
   - Kalman filter for trajectory prediction
   - Hungarian algorithm for detection-to-track association
   - Configuration: max_age=22 frames, min_hits=3, IOU threshold=0.3

3. **Counting Logic**
   - Two detection lines: upward traffic and downward traffic
   - Tracks vehicle center points crossing the lines
   - Maintains unique vehicle counts per lane and total

## Configuration

Key parameters in `main.py`:

```python
# Detection lines coordinates
line_up = [180, 410, 640, 410]     # Upward traffic line
line_down = [680, 400, 1280, 450]  # Downward traffic line

# SORT tracker parameters
tracker = Sort(max_age=22, min_hits=3, iou_threshold=0.3)

# Vehicle classes to detect
classNames = ["person", "bicycle", "car", "motorbike", "bus", "truck", ...]
```

## Dependencies

| Package | Version | Purpose |
|---------|---------|---------|
| ultralytics | >=8.3.220 | YOLOv8 implementation |
| opencv-python | >=4.11.0.86 | Video I/O and image processing |
| cvzone | >=1.6.1 | Computer vision utilities |
| numpy | >=2.3.4 | Numerical computing |
| filterpy | >=1.4.5 | Kalman filter for tracking |
| scikit-image | >=0.25.2 | Image processing |

## Customization

### Using Your Own Video

Replace `assets/traffic_cam.mp4` with your traffic camera footage, or modify the path in `main.py:11`:

```python
cap = cv2.VideoCapture("path/to/your/video.mp4")
```

### Adjusting Detection Lines

Modify the line coordinates in `main.py:26-27` to match your camera view:

```python
line_up = [x1, y1, x2, y2]
line_down = [x1, y1, x2, y2]
```

### Creating a Custom Mask

Generate a binary mask image where:
- White areas: analyzed for vehicles
- Black areas: ignored regions (noise)

Save as `assets/mask.png`

## Model Information

The project uses **YOLOv8-Large (yolov8l.pt)** - an 84MB pre-trained model trained on the COCO dataset with 80 object classes. The model is automatically downloaded by Ultralytics if not present.

## Performance

- **Detection**: ~30 FPS on GPU (varies by hardware)
- **Tracking**: Real-time on modern CPUs
- **Accuracy**: High precision for standard traffic scenarios

## License

This project is open source. See LICENSE file for details.

## Acknowledgments

- **YOLOv8**: Ultralytics for the object detection model
- **SORT**: Alex Bewley for the tracking algorithm
- **OpenCV**: For computer vision operations

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## Support

For issues and questions, please open an issue on GitHub.
