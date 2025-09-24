#!/bin/bash
set -e

# Usage: send_file.sh <local_file> <user> <host> <remote_path>

LOCAL_FILE=$1
USER=$2
HOST=$3
REMOTE_PATH=$4

if [[ -z "$LOCAL_FILE" || -z "$USER" || -z "$HOST" || -z "$REMOTE_PATH" ]]; then
    echo "Usage: $0 <local_file> <user> <host> <remote_path>"
    exit 1
fi

# Send file using sftp (passwordless recommended with ssh keys)
sftp -o StrictHostKeyChecking=no "$USER@$HOST:$REMOTE_PATH" <<< $"put $LOCAL_FILE"