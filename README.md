import joblib

# Paths to the saved files
model_file = 'sgd_classifier_model.pkl'
vectorizer_file = 'tfidf_vectorizer.pkl'
label_encoder_file = 'label_encoder.pkl'

# Load the saved model, vectorizer, and label encoder
classifier = joblib.load(model_file)
vectorizer = joblib.load(vectorizer_file)
label_encoder = joblib.load(label_encoder_file)

# Function to predict and check accuracy for a single input
def predict_and_check_accuracy(description, true_tag):
    # Transform the input description to TF-IDF features
    X_input = vectorizer.transform([description])
    
    # Predict the tag
    predicted_label = classifier.predict(X_input)[0]
    predicted_tag = label_encoder.inverse_transform([predicted_label])[0]
    
    # Check if prediction matches the true tag
    is_correct = (predicted_tag == true_tag)
    accuracy_percentage = 100 if is_correct else 0

    # Print results
    print(f"Description: '{description}'")
    print(f"Predicted Tag: {predicted_tag}")
    print(f"True Tag: {true_tag}")
    print(f"Accuracy: {accuracy_percentage:.2f}%")

    if accuracy_percentage > 50:
        print("The model's accuracy is above 50%.")
    else:
        print("The model's accuracy is 50% or below.")

# Example usage
input_description = "A sweet red fruit with seeds on the outside."
true_tag = "fruit"  # Replace with the actual true tag for this input
predict_and_check_accuracy(input_description, true_tag)
