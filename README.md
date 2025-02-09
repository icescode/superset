# Install Superset Di Ubuntu 22.04
Install Postgresql dan MySQL server
### Pastikan Python3 < Python 
```
sudo apt update
sudo apt-get install build-essential libssl-dev libffi-dev python3-dev python3-pip libsasl2-dev libldap2-dev default-libmysqlclient-dev
sudo apt install python3.10-venv
sudo apt install python3-dev libpq-dev

```
### Buat Python Environment
```
python3 -m venv supersetenv
. supersetenv/bin/activate
pip install marshmallow-enum psycopg2
pip install apache-superset
pip install mysqlclient
pip install pymysql
```

### Generate Secret key
```
openssl rand -base64 42 > secret.txt
```

### Edit Config File
```
nano /home/hardi/supersetenv/lib/python3.10/site-packages/superset/config.py
```
tambahkan
```
import pymysql
pymysql.install_as_MySQLdb()

SECRET_KEY = 'KEY HASIL GENERATE OPENSSL di secret.txt'
```

### buat superset.service
```
nano /etc/systemd/system/superset.service
```
```
Isikan entry di bawah ini
[Unit]
Description=Apache Superset instance daemon
After=network.target postgresql.service
Wants=mysql.service

[Service]
EnvironmentFile=/etc/environment
Environment="FLASK_APP=superset"
User=hardi
Group=hardi
Type=simple
ExecStart=/bin/bash -c 'source /home/hardi/supersetenv/bin/activate; superset run -p 8088 --with-threads --reload'
Restart=on-failure
RestartSec=5s
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

### Finishing
```
sudo systemctl daemon-reload
sudo systemctl start superset
sudo systemctl status superset
sudo systemctl enable superset
```
### Tambahkan Database Connection Di Superset untuk MySQL
```
mysql+pymysql://user:password@host:port/db?charset=utf8
```
