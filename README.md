from transformers import pipeline

# Load a pre-trained model for text classification
classifier = pipeline("zero-shot-classification", model="facebook/bart-large-mnli")

# Labels for classification
labels = ["DB Execution", "Other"]

def classify_input(input_text):
    result = classifier(input_text, candidate_labels=labels)
    return result["labels"][0]  # Returns the most likely label

# Example usage
input_text = "SELECT * FROM users WHERE id = 1"
print(classify_input(input_text))  # Output: "DB Execution"
