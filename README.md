import re

def match_pattern(input_string):
    pattern = r'^(HK|UK)[LSAWXZN][PVXG][ADIM][DPSTU].*$'
    if re.match(pattern, input_string):
        return True
    else:
        return False

# Example usage:
input_string = "HKLPAD123"
print(match_pattern(input_string))  # Output: True or False based on input_string
