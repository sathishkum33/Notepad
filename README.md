import re

# Example regex pattern
pattern = r'\b(?:hk|uk)[LSAWXZN][PVXG][ADIM][DPSTU][a-zA-Z0-9]*\b'

# Test strings
test_strings = [
    "hkLSADexample",
    "ukNPGDtest123",
    "hksvmdother",
    "UKLVIDmore",
    "ukPVIDextra",
    "hkwdatanything",
    "Ukxpud999"
]

# Function to check if the first six characters are either all uppercase or all lowercase
def is_same_case(s):
    return s[:6].islower() or s[:6].isupper()

# Find matches
matches = [s for s in test_strings if re.match(pattern, s) and is_same_case(s)]

print(matches)
