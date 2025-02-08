sudo semanage fcontext -a -t container_var_t "/new/storage/path(/.*)?"
sudo restorecon -Rv /new/storage/path