import pandas as pd
import numpy as np
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.linear_model import SGDClassifier
from sklearn.preprocessing import LabelEncoder
from sklearn.metrics import accuracy_score
import joblib

# File paths
file_path = 'output.csv'
model_file = 'sgd_classifier_model.pkl'
vectorizer_file = 'tfidf_vectorizer.pkl'
label_encoder_file = 'label_encoder.pkl'
test_file_path = 'test_data.csv'  # File containing test data

# Load the saved model, vectorizer, and label encoder
classifier = joblib.load(model_file)
vectorizer = joblib.load(vectorizer_file)
label_encoder = joblib.load(label_encoder_file)

# Load the test data
test_data = pd.read_csv(test_file_path)
test_descriptions = test_data['description'].values
test_tags = test_data['tag'].values

# Transform the test descriptions to TF-IDF features
X_test = vectorizer.transform(test_descriptions)

# Encode the true tags
y_true = label_encoder.transform(test_tags)

# Predict the tags
y_pred = classifier.predict(X_test)

# Calculate accuracy
accuracy = accuracy_score(y_true, y_pred)
accuracy_percentage = accuracy * 100

print(f"Accuracy: {accuracy_percentage:.2f}%")
