import pandas as pd
import numpy as np
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.linear_model import SGDClassifier
from sklearn.preprocessing import LabelEncoder
from sklearn.pipeline import make_pipeline
from sklearn.utils import shuffle

# Load data in chunks
def load_data_in_chunks(file_path, chunksize=10000):
    chunk_iter = pd.read_csv(file_path, chunksize=chunksize)
    for chunk in chunk_iter:
        yield chunk

# File path to the CSV file
file_path = 'path/to/your/large_dataset.csv'

# Initialize vectorizer and classifier
vectorizer = TfidfVectorizer()
classifier = SGDClassifier()
label_encoder = LabelEncoder()

# Define a variable to track if it's the first batch
first_batch = True

# Process the data in chunks
for chunk in load_data_in_chunks(file_path):
    # Extract descriptions and tags
    descriptions = chunk['description'].values
    tags = chunk['tag'].values
    
    # Encode the tags
    if first_batch:
        labels_encoded = label_encoder.fit_transform(tags)
    else:
        labels_encoded = label_encoder.transform(tags)
    
    # Transform descriptions to TF-IDF features
    X_batch = vectorizer.fit_transform(descriptions) if first_batch else vectorizer.transform(descriptions)
    y_batch = labels_encoded
    
    # Incrementally train the model
    if first_batch:
        classifier.partial_fit(X_batch, y_batch, classes=np.unique(labels_encoded))
        first_batch = False
    else:
        classifier.partial_fit(X_batch, y_batch)

# Example usage to predict tags for new descriptions
test_descriptions = ["A sweet red fruit with seeds on the outside.", "A long yellow fruit that monkeys love."]
X_test = vectorizer.transform(test_descriptions)
predicted_labels = classifier.predict(X_test)
predicted_tags = label_encoder.inverse_transform(predicted_labels)

for desc, tag in zip(test_descriptions, predicted_tags):
    print(f"Description: '{desc}' => Predicted Tag: {tag}")
