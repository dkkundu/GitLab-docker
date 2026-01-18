✅ README for GitLab Docker with External PostgreSQL
📌 Project: GitLab Docker (External PostgreSQL)

This repository contains a GitLab Docker deployment configured to use an external PostgreSQL database.

🚀 Features

✔️ GitLab running in Docker
✔️ External PostgreSQL database
✔️ Configured for production-style setup
✔️ Data stored on local volume

🧩 Requirements

Docker

Docker Compose (optional)

External PostgreSQL server reachable from GitLab container

Ubuntu / Debian host recommended

📌 Configuration (GitLab)
🔧 GitLab Configuration File

The GitLab configuration is stored in:

/etc/gitlab/gitlab.rb

✅ External PostgreSQL settings
gitlab_rails['db_adapter'] = 'postgresql'
gitlab_rails['db_encoding'] = 'utf8'
gitlab_rails['db_host'] = '192.168.68.176'
gitlab_rails['db_port'] = 5432
gitlab_rails['db_database'] = 'gitlabhq_production'
gitlab_rails['db_username'] = 'postgres'
gitlab_rails['db_password'] = 'dsdsd#1'

🛠️ Steps to Verify External PostgreSQL
✅ Check DB host configuration
docker exec -it gitlab bash -lc "grep -i \"db_host\" -n /etc/gitlab/gitlab.rb"


Expected output:

gitlab_rails['db_host'] = '192.168.68.176'

🧪 Test DB Connection
docker exec -it gitlab bash -lc "psql -h 192.168.68.176 -U postgres -d gitlabhq_production -c '\l'"

🔄 Reconfigure GitLab After Change

Whenever you change gitlab.rb, run:

docker exec -it gitlab gitlab-ctl reconfigure
docker exec -it gitlab gitlab-ctl restart

⚠️ Permissions Fix (if you change volume)

If you change the GitLab data volume, GitLab must have correct ownership:

sudo chown -R git:git /var/opt/gitlab/git-data
sudo chmod -R 2770 /var/opt/gitlab/git-data/repositories

✅ Check GitLab Data Directory Ownership
stat -c "%U:%G %n" /var/opt/gitlab/git-data/repositories


Expected output:

git:git /var/opt/gitlab/git-data/repositories

📌 Notes

The GitLab git user UID is usually 998.

External PostgreSQL must allow remote connections.

Make sure pg_hba.conf and postgresql.conf are properly configured.

🔐 PostgreSQL Remote Access Settings

On PostgreSQL server:

postgresql.conf
listen_addresses = '*'

pg_hba.conf
host all all 0.0.0.0/0 md5


Then restart PostgreSQL:

sudo systemctl restart postgresql

📌 Support

If you face any issue, run:

docker exec -it gitlab gitlab-ctl reconfigure


and share the error log.