# ai-climate-smart-crop-recommendation-system
AI-Based Climate-Smart Crop Recommendation System for farmer-friendly crop prediction using soil nutrients and climate parameters.
# AI-Based Climate-Smart Crop Recommendation System

## Theme
Climate Smart Agriculture

## Overview
The AI-Based Climate-Smart Crop Recommendation System is a farmer-friendly web application that helps users select suitable crops based on soil nutrients and climate conditions. The system uses machine learning to recommend crops using important agricultural parameters such as Nitrogen, Phosphorus, Potassium, temperature, humidity, rainfall, and pH.

## Problem Statement
Farmers often select crops based on traditional practices or assumptions. Due to climate change and changing environmental conditions, these methods are becoming less reliable. Wrong crop selection can reduce yield and affect farmer income. This project provides a smart and simple solution to support better agricultural decision-making.

## Objectives
- To develop an AI-based crop recommendation system
- To analyze soil and climate conditions
- To recommend suitable crops accurately
- To reduce crop planning risk
- To support sustainable agriculture
- To provide a farmer-friendly digital tool

## Features
- Crop recommendation using machine learning
- Farmer-friendly web interface
- Soil and climate based prediction
- Fast and simple result generation
- Supports climate-smart agriculture
- Can be extended with regional language support

## Input Parameters
- Nitrogen (N)
- Phosphorus (P)
- Potassium (K)
- Temperature
- Humidity
- Rainfall
- pH value

## Technology Stack
- Python
- Flask
- HTML
- CSS
- JavaScript
- Decision Tree Algorithm
- CSV Dataset

## System Workflow
1. User enters soil and climate values
2. Input is sent to Flask backend
3. Backend processes the data
4. Machine learning model predicts the suitable crop
5. Result is displayed to the user

## Theme Alignment
This project aligns with the theme **Climate Smart Agriculture** because it helps farmers choose crops based on environmental and soil conditions, supports climate-resilient farming, and encourages sustainable agriculture.

## Innovation
The uniqueness of this project lies in combining AI prediction with climate-smart farming support, farmer usability, and future inclusiveness such as regional language and voice guidance.

## Impact on Farmers
- Helps farmers make better crop decisions
- Reduces crop failure risk
- Improves productivity
- Supports rural livelihood
- Encourages scientific farming practices

## Accessibility and Inclusivity
The system is designed with a simple interface so that farmers with limited technical knowledge can use it easily. In future, the project can be enhanced with Kannada or other regional language support, voice assistance, and mobile accessibility.

## Future Scope
- Regional language support
- Voice-based guidance
- Weather API integration
- Fertilizer recommendation
- Yield prediction
- Mobile application support

## Team Members
- Raju G H
- V P Venkatesh
- Deepak N
- Dhiganth D

## Conclusion
This project is a practical and innovative climate-smart agriculture solution that supports farmers in selecting suitable crops using AI and environmental data.
code: main.py
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import LabelEncoder
from sklearn.tree import DecisionTreeClassifier
from sklearn.metrics import accuracy_score, classification_report

# Load the dataset
dataset_path = "Climate_Resilient_Crop_Dataset (1).csv"  # Ensure the dataset is in the same directory
df = pd.read_csv(dataset_path)

# Encode categorical target variable (crop names)
label_encoder = LabelEncoder()
df['Best Crop'] = label_encoder.fit_transform(df['Best Crop'])

# Define features and target variable
X = df.drop(columns=['Best Crop'])  # Features (climate conditions)
y = df['Best Crop']  # Target variable (crop)

# Split the dataset into training (80%) and testing (20%)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Train the Decision Tree Classifier
model = DecisionTreeClassifier(criterion='gini', max_depth=5, random_state=42)
model.fit(X_train, y_train)

# Make predictions
y_pred = model.predict(X_test)

# Evaluate the model with zero_division fix
accuracy = accuracy_score(y_test, y_pred)
report = classification_report(y_test, y_pred, labels=np.unique(y_test), zero_division=1)

print(f"Model Accuracy: {accuracy * 100:.2f}%")
print("\nClassification Report:\n", report)

# Predict a crop for custom climate input with feature names
sample_input = pd.DataFrame([[28, 300, 6.5, 80]], columns=X.columns)  # Ensuring proper feature names
predicted_crop = model.predict(sample_input)
print("Predicted Best Crop:", label_encoder.inverse_transform(predicted_crop))

and gui.py:-
import tkinter as tk
from tkinter import messagebox
from PIL import Image, ImageTk
import pandas as pd
import numpy as np
from sklearn.preprocessing import LabelEncoder
from sklearn.tree import DecisionTreeClassifier

# Load dataset
df = pd.read_csv("Climate_Resilient_Crop_Dataset (1).csv")
label_encoder = LabelEncoder()
df['Best Crop'] = label_encoder.fit_transform(df['Best Crop'])
X = df.drop(columns=['Best Crop'])
y = df['Best Crop']

# Train model
model = DecisionTreeClassifier(criterion='gini', max_depth=5, random_state=42)
model.fit(X, y)

# GUI Setup
root = tk.Tk()
root.title("Climate-Resilient Crop Predictor")
root.geometry("800x600")
root.resizable(False, False)

# Load background image
bg_img = Image.open("background.png").resize((800, 600))  # Use the background you generated
bg_photo = ImageTk.PhotoImage(bg_img)

canvas = tk.Canvas(root, width=800, height=600)
canvas.pack(fill="both", expand=True)
canvas.create_image(0, 0, image=bg_photo, anchor="nw")

# Input Labels and Entries
labels = ['Temperature (°C)', 'Rainfall (mm)', 'pH', 'Humidity (%)']
entries = []

for i, label in enumerate(labels):
    tk.Label(root, text=label, font=('Arial', 12), bg='lightblue').place(x=100, y=100 + i*40)
    entry = tk.Entry(root, font=('Arial', 12))
    entry.place(x=280, y=100 + i*40)
    entries.append(entry)

# Result Label
result_label = tk.Label(root, text="", font=('Arial', 16, 'bold'), bg='white', fg='green')
result_label.place(x=100, y=320)

# Crop logo label
logo_label = tk.Label(root, bg='white')
logo_label.place(x=550, y=150)

# Button hover animation
def on_enter(e): e.widget['bg'] = 'blue'; e.widget['fg'] = 'white'
def on_leave(e): e.widget['bg'] = 'white'; e.widget['fg'] = 'black'

# Predict Function
def predict_crop():
    try:
        values = [float(entry.get()) for entry in entries]
        input_df = pd.DataFrame([values], columns=X.columns)
        prediction = model.predict(input_df)
        crop = label_encoder.inverse_transform(prediction)[0]
        result_label.config(text=f"Recommended Crop: {crop}")

        # Show crop logo
        crop_logos = {
            'Rice': 'RICE.png',
            'Wheat': 'WHEAT.png',
            'Sugarcane': 'SUGERCANE.png'
        }

        logo_path = crop_logos.get(crop, 'crop_logo.png')
        logo_img = Image.open(logo_path).resize((100, 100))
        logo_photo = ImageTk.PhotoImage(logo_img)
        logo_label.config(image=logo_photo)
        logo_label.image = logo_photo


    except Exception as e:
        messagebox.showerror("Error", str(e))

# Predict Button
predict_btn = tk.Button(root, text="Predict Crop", font=('Arial', 14, 'bold'), command=predict_crop, bg='white')
predict_btn.place(x=280, y=260)
predict_btn.bind("<Enter>", on_enter)
predict_btn.bind("<Leave>", on_leave)

root.mainloop()

