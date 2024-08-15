# Real-time-face-detection-system


### 1. Install Required Libraries
- Make sure you have the necessary libraries installed. You can install them using pip:
```ruby
pip install opencv-python-headless flask
```
### 2. Create a Flask Application
   
- Create a new directory for your project and inside it, create a file named app.py. This file will contain your Flask application:

```ruby

from flask import Flask, render_template, Response
import cv2

app = Flask(__name__)

# Initialize the webcam
video_capture = cv2.VideoCapture(0)

def detect_faces(frame):
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    face_cascade = cv2.CascadeClassifier(cv2.data.haarcascades + 'haarcascade_frontalface_default.xml')
    faces = face_cascade.detectMultiScale(gray, 1.1, 4)

    for (x, y, w, h) in faces:
        cv2.rectangle(frame, (x, y), (x + w, y + h), (255, 0, 0), 2)

    return frame

def gen_frames():
    while True:
        success, frame = video_capture.read()
        if not success:
            break
        frame = detect_faces(frame)
        ret, buffer = cv2.imencode('.jpg', frame)
        frame = buffer.tobytes()
        yield (b'--frame\r\n'
               b'Content-Type: image/jpeg\r\n\r\n' + frame + b'\r\n')

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/video_feed')
def video_feed():
    return Response(gen_frames(),
                    mimetype='multipart/x-mixed-replace; boundary=frame')

if __name__ == '__main__':
    app.run(debug=True)
```

### 3. Run the Application
- Start your Flask application by running the following command in your project directory:
```ruby
python app.py

```

