import pandas as pd
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.linear_model import SGDClassifier
from sklearn.pipeline import make_pipeline
from sklearn.model_selection import train_test_split

# Sample function to generate large dataset (for demonstration purposes)
def generate_large_dataset(num_rows, num_labels):
    import random
    descriptions = [f"Description {i}" for i in range(num_rows)]
    labels = [f"label_{random.randint(0, num_labels-1)}" for _ in range(num_rows)]
    return descriptions, labels

# Generate a large dataset (replace this with your actual dataset)
num_rows = 1000000
num_labels = 10000
descriptions, labels = generate_large_dataset(num_rows, num_labels)

# Split the data into training and testing sets
X_train, X_test, y_train, y_test = train_test_split(descriptions, labels, test_size=0.2, random_state=42)

# Create a pipeline that transforms the data and then applies the classifier
model = make_pipeline(TfidfVectorizer(), SGDClassifier())

# Train the model
model.fit(X_train, y_train)

# Predict categories for new descriptions
test_descriptions = [
    "Description 1000001",
    "Description 1000002"
]

predicted_labels = model.predict(test_descriptions)

for desc, label in zip(test_descriptions, predicted_labels):
    print(f"Description: '{desc}' => Predicted Label: {label}")

# Evaluate the model
accuracy = model.score(X_test, y_test)
print(f"Accuracy: {accuracy:.2f}")
