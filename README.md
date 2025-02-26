#!/usr/bin/expect

set timeout 5
set oldpass "current_password"
set newpass "new_password"

spawn passwd
expect "Current password:"
send "$oldpass\r"
expect "New password:"
send "$newpass\r"
expect "Retype new password:"
send "$newpass\r"
expect eof