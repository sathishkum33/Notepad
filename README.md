import re

# Example regex pattern
pattern = r'\b(?:hk|uk)[LSAWXZN][PVXG][ADIM][DPSTU]\b'

# Test strings
test_strings = [
    "hkLSAD",
    "ukNPGD",
    "hksvmd",
    "UKLVID",
    "uKPVID",
    "hkwdat",
    "Ukxpud"
]

# Function to check if all letters are either uppercase or lowercase
def is_same_case(s):
    return s.islower() or s.isupper()

# Find matches
matches = [s for s in test_strings if re.match(pattern, s) and is_same_case(s)]

print(matches)
