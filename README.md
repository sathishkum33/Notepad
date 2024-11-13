#!/bin/bash

# Remove the old password (if it exists)
./metricbeat keystore remove ES_PASSWORD --force

# Automatically add the new password without prompt
echo "y" | ./metricbeat keystore add ES_PASSWORD <<EOF
new_password_here
EOF

echo "Password updated successfully in the keystore."