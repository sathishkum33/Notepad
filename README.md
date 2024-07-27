from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.naive_bayes import MultinomialNB
from sklearn.pipeline import make_pipeline

# Sample data
texts = ["I love programming.", "Python is great for data science.", "Machine learning is fascinating."]
labels = [1, 1, 0]  # 1: positive, 0: neutral

# Create a pipeline that transforms the data and then applies the classifier
model = make_pipeline(TfidfVectorizer(), MultinomialNB())

# Train the model
model.fit(texts, labels)

# Predict categories
predictions = model.predict(["I enjoy learning new things about AI."])
print(predictions)
