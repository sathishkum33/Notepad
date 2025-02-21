sudo semanage fcontext -a -t httpd_log_t "/apps/support/logs/httpd(/.*)?"
sudo restorecon -Rv /apps/support/logs/httpd
sudo chown -R 1000:1000 /apps/support/logs/httpd  # Ensure Apache user can access
sudo chmod -R 755 /apps/support/logs/httpd