import nltk
from nltk.tokenize import word_tokenize

nltk.download('punkt')

def contains_sql_keywords(input_text):
    sql_keywords = ["select", "insert", "update", "delete", "execute", "drop", "join", "create", "alter"]
    words = word_tokenize(input_text.lower())
    for keyword in sql_keywords:
        if keyword in words:
            return True
    return False

# Example usage
input_text = "SELECT * FROM users WHERE id = 1"
print(contains_sql_keywords(input_text))  # Output: True
