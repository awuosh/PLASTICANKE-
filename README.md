# PLASTICANKE-
Low-Cost Smartphone-Integrated Fluorescence Microscopy for Rapid Detection of Microplastic Pollution in Kenyan Waterways
PlastiScan-KE/
├── main.py                  # Kivy app (Android compatible)
├── image_processor.py       # OpenCV + thresholding for Nile Red
├── requirements.txt
├── buildozer.spec           # For building APK
├── README.md
└── assets/
    └── icon.png
# main.py - PlastiScan KE v1.0
from kivy.app import App
from kivy.uix.camera import Camera
from kivy.uix.boxlayout import BoxLayout
from kivy.uix.label import Label
from kivy.uix.button import Button
import cv2
import numpy as np
from datetime import datetime

class PlastiScanApp(App):
    def build(self):
        layout = BoxLayout(orientation='vertical')
        self.camera = Camera(play=True, resolution=(1280, 720))
        self.label = Label(text="Point at Nile Red sample → Press Count")
        self.button = Button(text="COUNT MICROPLASTICS")
        self.button.bind(on_press=self.count_plastics)
        
        layout.add_widget(self.camera)
        layout.add_widget(self.label)
        layout.add_widget(self.button)
        return layout

    def count_plastics(self, *args):
        # Capture frame
        frame = self.camera.texture.pixels
        # ... convert to OpenCV, threshold red channel, count blobs
        # Simple version:
        img = cv2.imread(frame)  # placeholder
        gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
        _, thresh = cv2.threshold(gray, 100, 255, cv2.THRESH_OTSU)
        contours, _ = cv2.findContours(thresh, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
        count = len([c for c in contours if cv2.contourArea(c) > 50])  # filter noise
        self.label.text = f"{count} particles detected!\n{datetime.now()}"
        
PlastiScanApp().run()
