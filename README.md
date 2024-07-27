import pandas as pd
import numpy as np
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.linear_model import SGDClassifier
from sklearn.preprocessing import LabelEncoder
from sklearn.calibration import CalibratedClassifierCV
from sklearn.metrics import accuracy_score, log_loss
from sklearn.utils import shuffle

# Load data in chunks
def load_data_in_chunks(file_path, chunksize=10000):
    chunk_iter = pd.read_csv(file_path, chunksize=chunksize)
    for chunk in chunk_iter:
        yield chunk

# File path to the CSV file
file_path = 'path/to/your/large_dataset.csv'

# Initialize vectorizer, classifier, and label encoder
vectorizer = TfidfVectorizer()
base_classifier = SGDClassifier(loss='log', average=True)  # Enable probability estimates
classifier = CalibratedClassifierCV(base_classifier, method='sigmoid', cv='prefit')
label_encoder = LabelEncoder()

# Define variables to track accuracy and loss
accuracies = []
losses = []

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
    if first_batch:
        X_batch = vectorizer.fit_transform(descriptions)
    else:
        X_batch = vectorizer.transform(descriptions)
    
    y_batch = labels_encoded
    
    # Incrementally train the model
    if first_batch:
        base_classifier.partial_fit(X_batch, y_batch, classes=np.unique(labels_encoded))
        classifier.fit(X_batch, y_batch)
        first_batch = False
    else:
        base_classifier.partial_fit(X_batch, y_batch)
        classifier.fit(X_batch, y_batch)
    
    # Predict on the current batch
    predictions = classifier.predict(X_batch)
    pred_proba = classifier.predict_proba(X_batch)
    
    # Calculate accuracy and log loss
    acc = accuracy_score(y_batch, predictions)
    loss = log_loss(y_batch, pred_proba)
    
    # Save accuracy and loss
    accuracies.append(acc)
    losses.append(loss)
    
    print(f"Batch Accuracy: {acc:.4f}, Batch Loss: {loss:.4f}")

# Example usage to predict tags for new descriptions
test_descriptions = ["A sweet red fruit with seeds on the outside.", "A long yellow fruit that monkeys love."]
X_test = vectorizer.transform(test_descriptions)
predicted_labels = classifier.predict(X_test)
predicted_tags = label_encoder.inverse_transform(predicted_labels)

# Save predictions to a file
predictions_df = pd.DataFrame({'description': test_descriptions, 'predicted_tag': predicted_tags})
predictions_df.to_csv('predictions.csv', index=False)

for desc, tag in zip(test_descriptions, predicted_tags):
    print(f"Description: '{desc}' => Predicted Tag: {tag}")

# Print final accuracy and loss details
print(f"Final Accuracy: {np.mean(accuracies):.4f}")
print(f"Final Log Loss: {np.mean(losses):.4f}")
