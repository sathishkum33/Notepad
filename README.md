import joblib
import numpy as np

# Paths to the saved files
model_file = 'sgd_classifier_model.pkl'
vectorizer_file = 'tfidf_vectorizer.pkl'
label_encoder_file = 'label_encoder.pkl'

# Load the saved model, vectorizer, and label encoder
classifier = joblib.load(model_file)
vectorizer = joblib.load(vectorizer_file)
label_encoder = joblib.load(label_encoder_file)

# Function to predict and provide confidence score for a single input
def predict_with_confidence(description):
    # Transform the input description to TF-IDF features
    X_input = vectorizer.transform([description])
    
    # Predict the tag and get the confidence scores
    predicted_label = classifier.predict(X_input)[0]
    predicted_tag = label_encoder.inverse_transform([predicted_label])[0]
    
    if hasattr(classifier, "predict_proba"):
        confidence_scores = classifier.predict_proba(X_input)[0]
    else:
        confidence_scores = classifier.decision_function(X_input)
        confidence_scores = np.exp(confidence_scores) / np.sum(np.exp(confidence_scores))
    
    confidence_percentage = np.max(confidence_scores) * 100

    # Print results
    print(f"Description: '{description}'")
    print(f"Predicted Tag: {predicted_tag}")
    print(f"Confidence: {confidence_percentage:.2f}%")

    if confidence_percentage > 50:
        print("The model's confidence is above 50%.")
    else:
        print("The model's confidence is 50% or below.")

# Example usage
input_description = "A sweet red fruit with seeds on the outside."
predict_with_confidence(input_description)
