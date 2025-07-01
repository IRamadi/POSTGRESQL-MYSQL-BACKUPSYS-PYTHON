📂 Database Backup Service
A robust, automated backup solution for PostgreSQL & MySQL

🚀 Features
✅ Automated Backups (SQL & CSV formats)
✅ Scheduled Backups (hourly/daily/weekly)
✅ Backup Retention (keeps last 3 backups)
✅ Web API (Flask-based, port 5002)
✅ Windows Service Control (start/stop PostgreSQL/MySQL)
✅ Cross-Platform (Windows, Linux, macOS)

🛠 Installation
1. Clone the Repository
bash
git clone https://github.com/yourusername/database-backup-service.git
cd database-backup-service
2. Install Dependencies
bash
pip install -r requirements.txt
3. Install Database Tools (If Missing)
For PostgreSQL
bash
# Linux (Debian/Ubuntu)
sudo apt-get install postgresql-client

# macOS (Homebrew)
brew install postgresql
For MySQL
bash
# Linux (Debian/Ubuntu)
sudo apt-get install mysql-client

# macOS (Homebrew)
brew install mysql-client
🏃 Running the Service
Basic Usage
bash
python db_backup_service.py
➡ Access API: http://localhost:5002

Run in Background (Linux/macOS)
bash
nohup python db_backup_service.py > backup_service.log 2>&1 &
Run as a Windows Service
Install pywin32:

bash
pip install pywin32
Use NSSM (Non-Sucking Service Manager):

powershell
nssm install "Database Backup Service" "C:\Python\python.exe" "C:\path\to\db_backup_service.py"
nssm start "Database Backup Service"
🔌 API Endpoints
Endpoint	Method	Description
/	GET	Service status
/connect	POST	Connect to a database
/disconnect	POST	Disconnect from DB
/create_backup	POST	Create a backup
/restore_backup	POST	Restore a backup
/list_backups	GET	List available backups
/set_schedule	POST	Set backup schedule
/service_control	POST	Control DB service (Windows)
📌 See Full API Documentation for details.

⚙ Configuration
Config file (db_backup_config.ini) is auto-generated on first run:

ini
[Database]
type = postgresql
host = localhost
port = 5432
name = mydb
user = admin
password = password

[Backup]
location = /backups
format = sql
schedule = daily
📜 Backup Policy
Keeps only the last 3 backups (oldest auto-deleted).

Backup naming: Backup_{db_name}_{YYYYMMDD_HHMMSS}.sql (or .zip for CSV).

📂 Logging
Logs are stored in:

text
logs/db_backup_service.log
Rotates logs (max 5 files, 1MB each).

🔧 Troubleshooting
Issue	Fix
"pg_dump not found"	Install PostgreSQL client tools.
"Access denied" (Windows)	Run as Administrator.
Backup fails	Check logs in logs/.
API not responding	Ensure service is running (http://localhost:5002).


🌐 API Endpoints Reference
Endpoint	Method	Description	Example Request Body
/	GET	Check service status ({"status": "running"})	-
/connect	POST	Connect to PostgreSQL/MySQL	{"db_type": "postgresql", "host": "localhost", "port": "5432", "db_name": "mydb", "user": "admin", "password": "pass"}
/disconnect	POST	Disconnect from the current database	-
/create_backup	POST	Create a new backup (SQL or CSV)	{"backup_dir": "/backups", "format": "sql"}
/restore_backup	POST	Restore a backup file	{"backup_file": "/backups/Backup_mydb_20240101.sql"}
/list_backups	GET	List available backups in a directory (query param: ?backup_dir=/backups)	-
/set_schedule	POST	Set backup schedule (hourly, daily, weekly, disabled)	{"schedule": "daily"}
/service_control	POST	(Windows only) Start/stop/restart PostgreSQL/MySQL services	{"service_type": "postgresql", "action": "restart"}
🔄 Example API Usage
1. Connect to a PostgreSQL Database
bash
curl -X POST http://localhost:5002/connect \
  -H "Content-Type: application/json" \
  -d '{
    "db_type": "postgresql",
    "host": "localhost",
    "port": "5432",
    "db_name": "mydb",
    "user": "admin",
    "password": "password"
  }'
2. Create a Backup (SQL Format)
bash
curl -X POST http://localhost:5002/create_backup \
  -H "Content-Type: application/json" \
  -d '{
    "backup_dir": "/backups",
    "format": "sql"
  }'
3. List Backups
bash
curl "http://localhost:5002/list_backups?backup_dir=/backups"
4. Restore a Backup
bash
curl -X POST http://localhost:5002/restore_backup \
  -H "Content-Type: application/json" \
  -d '{
    "backup_file": "/backups/Backup_mydb_20240101.sql"
  }'
🔍 How the API Works
The script runs a Flask server on port 5002 by default.

All endpoints accept/return JSON.

Error handling: If something fails, the API returns a 400/500 error with details.

📌 Key Notes
Authentication: The API has no built-in auth (for simplicity). If exposed to the internet, secure it with:

Firewall rules (allow only trusted IPs).

Reverse proxy (NGINX/Apache with HTTPS + Basic Auth).

Or modify app.py to add Flask-based authentication.

Windows Service Control:

Only works on Windows (uses pywin32).

Requires admin privileges to manage services.
