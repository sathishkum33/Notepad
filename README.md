✅ 1. Backup Your Database

Before anything else, make a full backup to avoid data loss.

cd /path/to/old-neo4j
bin/neo4j stop   # stop Neo4j if it's running

# Optional: backup using neo4j-admin
bin/neo4j-admin dump --database=neo4j --to=/path/to/backup/neo4j.dump

Alternatively, copy the entire data/databases and data/transactions directories:

cp -r data/databases /path/to/backup/
cp -r data/transactions /path/to/backup/


---

✅ 2. Download the New Version

Download the new tarball from Neo4j Download Center.

wget https://neo4j.com/artifact.php?name=neo4j-community-5.x.x-unix.tar.gz -O neo4j-community-new.tar.gz
tar -xvzf neo4j-community-new.tar.gz
mv neo4j-community-5.x.x /opt/neo4j-new


---

✅ 3. Compare and Merge Configuration Files

Carefully migrate config changes from your old version to the new one.

diff /old-neo4j/conf/neo4j.conf /opt/neo4j-new/conf/neo4j.conf

Manually copy or merge custom settings like:

dbms.default_listen_address

dbms.connector.bolt.listen_address

dbms.connector.http.listen_address

dbms.security.auth_enabled

Any memory or performance settings you changed.



---

✅ 4. Move the Data

If you’re not restoring from a dump, copy the data directories to the new version:

cp -r /old-neo4j/data/databases /opt/neo4j-new/data/
cp -r /old-neo4j/data/transactions /opt/neo4j-new/data/

> ⚠️ Important: Neo4j may auto-upgrade the data format if moving from a major version (e.g., 4.x to 5.x), but you cannot downgrade after this.




---

✅ 5. Start the New Version

cd /opt/neo4j-new
bin/neo4j start

Check logs in logs/neo4j.log and logs/debug.log to ensure startup is successful.


---

✅ 6. Test Thoroughly

Use cypher-shell or Browser to query data.

Monitor logs for warnings.

Validate custom plugins or procedures if any (they may need updates).



---

🔁 Optional: Restore from Dump (if preferred)

bin/neo4j-admin load --from=/path/to/backup/neo4j.dump --database=neo4j --force

