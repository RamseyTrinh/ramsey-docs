# How to run fci bss portal

## Prerequisites

Clone 2 sources: UI and API

## Run portal-bss-ui
Install pnpm

```bash
nodejs: 16.14
pnpm: 8.9

pnpm install
```
## Run portal-bss-api
Recommended to use Ubuntu or WSL to run Portal-bss-api
### Setup Kong Gateway

Change docker-compose.yml:
```bash
# Change image
quay.io/keycloak/keycloak:16.1.1
# command this line
command: --default-authentication-plugin=mysql_native_password
```
Change kong.yml:
```bash
WSL

cp kong_template.yml kong.yml
# in kong.yml
# command service API internal
# command service TERRAFORM 
# command plugins
```
Run docker compose
```bash
WSL

set KONG_TARGET_SVC_ADDR="host.docker.internal"
sed -i 's/{KONG_TARGET_SVC_ADDR}/'"$KONG_TARGET_SVC_ADDR"'/g' kong.yml

```bash
CMD
# Start DB first as some some services may fail to access DB early

docker-compose up -d db
sleep 5
docker-compose up -d

# Check if container DB is running or not
# If not, try to delete volumn of the container
```
### Import Database
After run container successfully, go to localhost:8002 

- Go to Keycloak admin portal (localhost:8002, account admin/abc123)
- Import 3 file sql to dtb: keycloak, user, iaas-vmw

### Install requirements for 2 service
- portal-api-svc-iaas-vmw
Config file requirements.txt
```bash
Flask==1.1.4
markupsafe==2.0.1
Flask-RESTful==0.3.8
Flask-SQLAlchemy==2.4.4
SQLAlchemy-Utils==0.36.8
Flask-UUID==0.2
Flask-HTTPAuth==4.2.0
Flask-Session==0.3.2
Flask-Cors==3.0.10
Flask-Executor==0.9.4
psycopg2-binary==2.8.6
mysqlclient==2.0.3
Flask-Migrate==2.7.0
alembic==1.9.4
flask-sqlacodegen==1.1.8
Flask-Bcrypt==0.7.1
Flask-Babel==2.0.0
Flask-Caching==1.10.0
flask-oidc==1.4.0
python3-saml==1.10.1
PyJWT==2.5.0
python-dateutil==2.8.1
Flask-Limiter==1.4
urlquote==1.1.4
webargs==7.0.1
paramiko==2.7.2
requests==2.25.1
redis==4.5.2
cryptography==3.4.6
APScheduler==3.7.0
flask-swagger==0.2.14
flask-swagger-ui==3.36.0
python-keycloak==0.24.0
orjson
marshmallow-union==0.1.15.post1
munch==2.5.0
fiql-parser==0.15
SQLAlchemy==1.3.23
Deprecated==1.2.12
celery
celery[redis]
# This service only
pyvcloud==23.0.0
xmltodict==0.12.0
pydantic==1.9.1
python-slugify==5.0.2
sentry-sdk==1.1.0
sentry-sdk[flask]==1.1.0
python-dotenv==0.17.1
blinker
newrelic==9.5.0
asyncio==3.4.3
click==7.1.2
pyyaml
minio==7.1.0
pydash==5.0.1
toolz==0.11.1
dictdiffer==0.8.1
freezegun==1.4.0
ipaddr==2.2.0
google-cloud-logging==2.6.0
boto3==1.18.39
avisdk==22.1.3
# openstacksdk
# pyOpenSSL
openstacksdk==0.59.0
dependency_injector
pyOpenSSL==21.0.0
pyhumps==3.7.1
pyvmomi==7.0.3
elasticsearch==8.1.0
flasgger==0.9.5
pyexcel==0.7.0
pyexcel-xlsx==0.6.0
openpyxl==3.0.10
Pillow==9.2.0
pandas==1.4.2
kubernetes==23.3.0
netaddr==0.8.0
protobuf>=3.20.1
Flask-Pydantic==0.9.0
pyTelegramBotAPI==4.7.0
deepdiff==6.2.2
casbin==1.34.0
casbin_sqlalchemy_adapter==0.5.0
# Dependency with SQLAlchemy
# From version 5.3.0, ít depend on SQLAlchemy 2.0 and higher
kombu==5.2.4
taskflow==5.2.0
beautifulsoup4==4.12.2
html2jirawiki==1.0.0
lxml==4.9.2
opentelemetry-api==1.22.0
opentelemetry-sdk==1.22.0
opentelemetry-instrumentation==0.43b0
opentelemetry-instrumentation-celery==0.43b0
opentelemetry-instrumentation-flask==0.43b0
opentelemetry-exporter-otlp==1.22.0
opentelemetry-instrumentation-sqlalchemy==0.43b0
opentelemetry-instrumentation-requests==0.43b0
opentelemetry-instrumentation-system-metrics==0.43b0
ruamel.yaml==0.17.40
firebase-admin==6.2.0
croniter==2.0.1
pydantic[email]==1.9.1
jsonpath-tp==1.0.1
numpy==1.24.3
tzdata==2024.1
supabase==0.7.1
httpx==0.23.3
```
Install
```bash
python3 -m venv venv 
venv/bin/activate
pip install -r requirements.txt 
pip install -r requirements-dev.txt
```
- portal-api-svc-user
```bash
Flask==1.1.4
markupsafe==2.0.1
Flask-RESTful==0.3.8
Flask-SQLAlchemy==2.4.4
SQLAlchemy-Utils==0.36.8
Flask-UUID==0.2
Flask-HTTPAuth==4.2.0
Flask-Session==0.3.2
Flask-Cors==3.0.10
Flask-Executor==0.9.4
psycopg2-binary==2.9.9
mysqlclient
Flask-Migrate==2.7.0
flask-sqlacodegen==1.1.8
Flask-Bcrypt==0.7.1
flask-babel==2.0.0
Flask-Caching==1.10.0
flask-oidc==1.4.0
python3-saml==1.10.1
PyJWT==2.0.1
python-dateutil==2.8.1
Flask-Limiter==1.4
urlquote==1.1.4
webargs==7.0.1
paramiko==2.7.2
requests==2.25.1
redis==3.5.3
cryptography==3.4.6
APScheduler==3.7.0
flask-swagger==0.2.14
flask-swagger-ui==3.36.0
python-keycloak==0.24.0
orjson
marshmallow-union
sentry-sdk==1.0.0
munch==2.5.0
fiql-parser==0.15
SQLAlchemy==1.3.23
Deprecated==1.2.12
python-dotenv
pydash==5.0.1
python-slugify==5.0.2
casbin==1.17.6
casbin_sqlalchemy_adapter==0.5.0
pydantic==1.8.1
pandas
numpy
openpyxl==3.0.10
lxml
xmlsec
```
Install
```bash
python3 -m venv venv 
venv/bin/activate
pip install -r requirements.txt 
pip install -r requirements-dev.txt
```
### Run 2 services
```bash
python index_dev.py
```






