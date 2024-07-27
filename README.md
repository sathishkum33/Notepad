from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.naive_bayes import MultinomialNB
from sklearn.pipeline import make_pipeline

# Sample data
descriptions = [
    "A sweet red fruit with seeds on the outside.",
    "A long yellow fruit that monkeys love.",
    "A small red fruit that is often used in pies.",
    "A round citrus fruit that is orange in color."
]
labels = ["strawberry", "banana", "cherry", "orange"]

# Create a pipeline that transforms the data and then applies the classifier
model = make_pipeline(TfidfVectorizer(), MultinomialNB())

# Train the model
model.fit(descriptions, labels)

# Predict categories for new descriptions
test_descriptions = [
    "This fruit is yellow and curved.",
    "It is a small red fruit used in desserts.",
    "This fruit is juicy and has a tough orange skin.",
    "It is red and has seeds on the outside."
]

predicted_fruits = model.predict(test_descriptions)

for desc, fruit in zip(test_descriptions, predicted_fruits):
    print(f"Description: '{desc}' => Predicted Fruit: {fruit}")
