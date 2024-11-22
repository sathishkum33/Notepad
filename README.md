#!/bin/bash

# Configurations
JASYPT_HOME="/path/to/jasypt"  # Replace with your Jasypt installation path
PASSWORD="your_master_password" # Replace with your secret master password
ALGORITHM="PBEWithMD5AndDES"    # Replace with your desired encryption algorithm

# Jasypt CLI scripts
ENCRYPT_SCRIPT="$JASYPT_HOME/bin/encrypt.sh"
DECRYPT_SCRIPT="$JASYPT_HOME/bin/decrypt.sh"

# Inputs
DATA_TO_ENCRYPT="SensitiveData123"
ENCRYPTED_FILE="encrypted_data.txt"
DECRYPTED_FILE="decrypted_data.txt"

# Encrypt
echo "Encrypting data..."
ENCRYPTED_TEXT=$($ENCRYPT_SCRIPT input="$DATA_TO_ENCRYPT" password="$PASSWORD" algorithm="$ALGORITHM")
echo "Encrypted Text: $ENCRYPTED_TEXT"

# Save encrypted text to a file
echo "$ENCRYPTED_TEXT" > "$ENCRYPTED_FILE"

# Decrypt
echo "Decrypting data..."
DECRYPTED_TEXT=$($DECRYPT_SCRIPT input="$ENCRYPTED_TEXT" password="$PASSWORD" algorithm="$ALGORITHM")
echo "Decrypted Text: $DECRYPTED_TEXT"

# Save decrypted text to a file
echo "$DECRYPTED_TEXT" > "$DECRYPTED_FILE"

# Output files
echo "Encrypted data saved to $ENCRYPTED_FILE"
echo "Decrypted data saved to $DECRYPTED_FILE"