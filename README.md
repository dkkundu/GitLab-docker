# GitLab Docker Deployment with External PostgreSQL

This repository provides a ready-to-use [Docker Compose](https://docs.docker.com/compose/) setup for running GitLab CE with an **external PostgreSQL database**. It is designed for production-style deployments with persistent storage and easy configuration.

---

## 🚀 Features

- GitLab CE running in Docker
- Uses an external PostgreSQL database (not bundled)
- Data stored on local Docker volumes
- Easy configuration and reconfiguration
- Example for production-like environments

---

## 🧩 Requirements

- Docker
- Docker Compose
- External PostgreSQL server (reachable from the GitLab container)
- Linux host (Ubuntu/Debian recommended)

---

## 📦 Setup & Configuration

1. **Clone this repository:**
	 ```sh
	 git clone <this-repo-url>
	 cd gitlab
	 ```

2. **Edit `docker-compose.yml` as needed:**
	 - Update `gitlab_rails['db_host']`, `db_username`, `db_password`, etc. in the `environment` section to match your PostgreSQL server.
	 - Example (replace with your actual values):
		 ```yaml
		 gitlab_rails['db_host'] = '<YOUR_PG_HOST>'
		 gitlab_rails['db_port'] = 5432
		 gitlab_rails['db_database'] = '<YOUR_PG_DATABASE>'
		 gitlab_rails['db_username'] = '<YOUR_PG_USER>'
		 gitlab_rails['db_password'] = '<YOUR_PG_PASSWORD>'
		 ```
- **Test DB connection from container:**
	```sh
	docker exec -it gitlab bash -lc "psql -h <PG_HOST> -U <PG_USER> -d <PG_DB> -c '\l'"
	# Example: docker exec -it gitlab bash -lc "psql -h 192.168.1.100 -U gitlabuser -d gitlabdb -c '\l'"
	```

3. **Start GitLab:**
	 ```sh
	 docker compose up -d
	 ```

4. **Access GitLab:**
	 - Web: [http://localhost:8080](http://localhost:8080) (or your configured hostname)
	 - SSH: Port 2224 (as mapped in `docker-compose.yml`)

---

## ⚙️ PostgreSQL Server Configuration

Ensure your PostgreSQL server allows remote connections:

1. **Edit `postgresql.conf`:**
	 ```
	 listen_addresses = '*'
	 ```
2. **Edit `pg_hba.conf`:**
	 ```
	 host all all 0.0.0.0/0 md5
	 ```
3. **Restart PostgreSQL:**
	 ```sh
	 sudo systemctl restart postgresql
	 ```

---

## 🛠️ Useful Commands

- **Check DB host in config:**
	```sh
	docker exec -it gitlab bash -lc "grep -i 'db_host' /etc/gitlab/gitlab.rb"
	```
- **Test DB connection from container:**
	```sh
	docker exec -it gitlab bash -lc "psql -h <PG_HOST> -U <PG_USER> -d <PG_DB> -c '\l'"
	```
- **Reconfigure GitLab after config changes:**
	```sh
	docker exec -it gitlab gitlab-ctl reconfigure
	docker exec -it gitlab gitlab-ctl restart
	```
- **Fix permissions if you change volumes:**
	```sh
	sudo chown -R git:git /var/opt/gitlab/git-data
	sudo chmod -R 2770 /var/opt/gitlab/git-data/repositories
	```
- **Check data directory ownership:**
	```sh
	stat -c "%U:%G %n" /var/opt/gitlab/git-data/repositories
	# Should output: git:git /var/opt/gitlab/git-data/repositories
	```

---

## 📝 Notes & Troubleshooting

- The default GitLab `git` user UID is usually 998.
- Ensure your PostgreSQL server is accessible from the Docker host network.
- If you encounter issues, check logs with:
	```sh
	docker exec -it gitlab gitlab-ctl tail
	```
- After any config change, always reconfigure and restart GitLab.
- For more help, see [GitLab Docker documentation](https://docs.gitlab.com/omnibus/docker/).

---

## 📬 Support

If you face any issues, please open an issue in this repository or consult the official GitLab documentation.