import fasttext

# Train a model (assuming you have training data in 'train.txt' with labels '__label__db_execution' and '__label__other')
# model = fasttext.train_supervised(input="train.txt")

# Load a pre-trained model
# model = fasttext.load_model("model.bin")

def classify_input(input_text):
    prediction = model.predict(input_text)
    return prediction[0][0]  # Returns the most likely label

# Example usage (with a pre-trained model)
# input_text = "SELECT * FROM users WHERE id = 1"
# print(classify_input(input_text))  # Output: "__label__db_execution"
