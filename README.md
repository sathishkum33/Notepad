import numpy as np
import pandas as pd
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.linear_model import SGDClassifier
from sklearn.preprocessing import LabelEncoder
from sklearn.pipeline import make_pipeline
from sklearn.utils import shuffle

# Function to generate large dataset (for demonstration purposes)
def generate_large_dataset(num_rows, num_labels):
    import random
    descriptions = [f"Description {i}" for i in range(num_rows)]
    labels = [f"label_{random.randint(0, num_labels-1)}" for _ in range(num_rows)]
    return descriptions, labels

# Generate a large dataset (replace this with your actual dataset)
num_rows = 1000000
num_labels = 10000
descriptions, labels = generate_large_dataset(num_rows, num_labels)

# Shuffle the data
descriptions, labels = shuffle(descriptions, labels, random_state=42)

# Encode the labels
label_encoder = LabelEncoder()
labels_encoded = label_encoder.fit_transform(labels)

# Create a TfidfVectorizer instance
vectorizer = TfidfVectorizer()

# Create a SGDClassifier instance
classifier = SGDClassifier()

# Define batch size
batch_size = 10000

# Process the data in batches
for start in range(0, num_rows, batch_size):
    end = start + batch_size
    X_batch = vectorizer.fit_transform(descriptions[start:end])
    y_batch = labels_encoded[start:end]

    if start == 0:
        # For the first batch, use the full fit method
        classifier.partial_fit(X_batch, y_batch, classes=np.unique(labels_encoded))
    else:
        # For subsequent batches, use partial_fit
        classifier.partial_fit(X_batch, y_batch)

# To evaluate the model, generate a test set
test_descriptions = ["Description 1000001", "Description 1000002"]
X_test = vectorizer.transform(test_descriptions)
predicted_labels = classifier.predict(X_test)
predicted_labels = label_encoder.inverse_transform(predicted_labels)

for desc, label in zip(test_descriptions, predicted_labels):
    print(f"Description: '{desc}' => Predicted Label: {label}")
