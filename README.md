import joblib

# Paths to the saved files
model_file = 'sgd_classifier_model.pkl'
vectorizer_file = 'tfidf_vectorizer.pkl'
label_encoder_file = 'label_encoder.pkl'

# Load the saved model, vectorizer, and label encoder
classifier = joblib.load(model_file)
vectorizer = joblib.load(vectorizer_file)
label_encoder = joblib.load(label_encoder_file)

# Example usage to predict tags for new descriptions
test_descriptions = ["A sweet red fruit with seeds on the outside.", "A long yellow fruit that monkeys love."]
X_test = vectorizer.transform(test_descriptions)
predicted_labels = classifier.predict(X_test)
predicted_tags = label_encoder.inverse_transform(predicted_labels)

for desc, tag in zip(test_descriptions, predicted_tags):
    print(f"Description: '{desc}' => Predicted Tag: {tag}")
