#!/bin/bash

# Check if JAVA_HOME is set; if not, try to use `java` from PATH
if [ -z "$JAVA_HOME" ]; then
  JAVA_EXEC="java"
else
  JAVA_EXEC="$JAVA_HOME/bin/java"
fi

# Path to Jasypt CLI jar (update this path if necessary)
JASYPT_CLI_JAR="/path/to/jasypt/lib/jasypt-1.9.3.jar"

# Check if the Jasypt JAR exists
if [ ! -f "$JASYPT_CLI_JAR" ]; then
  echo "Error: Jasypt CLI JAR not found at $JASYPT_CLI_JAR"
  exit 1
fi

# Parse input arguments
for arg in "$@"; do
  case $arg in
    input=*)
      INPUT="${arg#*=}"
      ;;
    password=*)
      PASSWORD="${arg#*=}"
      ;;
    algorithm=*)
      ALGORITHM="${arg#*=}"
      ;;
    *)
      echo "Unknown argument: $arg"
      ;;
  esac
done

# Validate required arguments
if [ -z "$INPUT" ] || [ -z "$PASSWORD" ]; then
  echo "Usage: $0 input=<text_to_encrypt> password=<encryption_password> [algorithm=<encryption_algorithm>]"
  exit 1
fi

# Set default algorithm if not provided
if [ -z "$ALGORITHM" ]; then
  ALGORITHM="PBEWithMD5AndDES"
fi

# Execute the encryption
$JAVA_EXEC -cp "$JASYPT_CLI_JAR" org.jasypt.intf.cli.JasyptPBEStringEncryptionCLI \
  input="$INPUT" password="$PASSWORD" algorithm="$ALGORITHM"