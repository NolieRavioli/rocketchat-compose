This Markdown document copies the info from 
[Deploy with Docker Docker Compose](/Deploy%20with%20Docker%20Docker%20Compose.pdf)

```
Deploy Rocket.Chat using Docker Compose
Update Rocket.Chat to a newer version
Update MongoDB to a compatible version on Podman
Back up and restore your MongoDB database
Configure Rocket.Chat using environment variables on Docker
```
## Step 1: Install Docker, Docker Compose and Git

```
This guide is intended for Linux users. Users on macOS or Windows can install
Docker Desktop instead.
```
```
1. Ensure you have Docker, Docker Compose (Docker Compose v2 is
required) and Git installed and operational on your system. If you don't
have them installed, you can conveniently set them up in Linux
distributions with the command below:
```
# Deploy with Docker & Docker Compose

```
As of December 15, 2023, Rocket.Chat has ceased support for
connections from cloud services and of&cial mobile/desktop apps to
workspaces running legacy versions outside our support window. Users
on unsupported legacy servers are advised to upgrade to the latest
Rocket.Chat version to ensure continued access to cloud, mobile, and
desktop applications. Each Rocket.Chat version is supported for six
months post-release.
```
```
If you’re using the built in Bitnami MongoDB images, refer to this forum
post to upgrade to the of&cial MongoDB 8.2 image required for
Rocket.Chat 8.0.
```

```
2. To run Docker commands without using sudo, add your current user to
the Docker group.
a. Find your current username with this command:
```
```
b. Add your user to the docker group:
```
```
You can replace $USER with the username from the previous
step.
c. Finally, reboot the system to for the changes to take effect:
```
## Step 2: Fetch the Rocket.Chat compose

## conguration les

The of&cial rocketchat-compose repository contains all the con&guration &les
you need to successfully deploy Rocket.Chat using Docker Compose.

```
curlcurl -L https://get.docker.com -L https://get.docker.com || sh sh # Install Docker# Install Docker
sudosudo aptapt installinstall gitgit # Install Git# Install Git
```
```
Bash Copy
```
```
For other OS or alternative installation methods, refer to the
of&cial Docker documentation and Git documentation.
```
```
whoamiwhoami
```
```
Bash Copy
```
```
sudosudo usermodusermod -aG docker -aG docker $USER$USER
```
```
Bash Copy
```
```
sudosudo rebootreboot
```
```
Bash Copy
```
```
If your server has an active &rewall, you may need to whitelist speci&c
URLs to allow communication with Rocket.Chat's cloud services (e.g.,
push noti&cations, marketplace). Refer to the Firewall Conguration
documentation for the complete list of required URLs.
```

```
1. Clone the repository using Git and navigate to the cloned directory:
```
```
This repository includes:
a. compose.yml : The main con&guration &le for Docker Compose
to deploy Rocket.Chat.
b. .env.example : An example enviroment &le for customizing your
deployment.
c. Additional con&gs: Other con&guration &les to support your
deployment, such as Trae&k (for reverse proxy and HTTPS) and
monitoring tools.
2. To con&gure your deployment without editing the compose.yml, you’ll
need a dedicated .env &le. Copy the example &le to start your
customization:
```
```
This command creates the .env &le where you can now de&ne your
deployment variables, such as the desired Rocket.Chat version and your
workspace URL.
```
## Step 3: Congure Rocket.Chat

Before launching your Rocket.Chat workspace, you must con&gure some key
variables in the .env &le you created. Start by opening the .env &le:

### Set Rocket.Chat version

Modify the RELEASE variable to specify the exact version of Rocket.Chat you
wish to deploy. Refer to the Rocket.Chat releases page for a list of available
stable versions.

```
gitgit clone --depth 1clone --depth 1
https://github.com/RocketChat/rocketchat-compose.githttps://github.com/RocketChat/rocketchat-compose.git
cdcd rocketchat-composerocketchat-compose
```
```
Bash Copy
```
```
cpcp .env.example .env.env.example .env
```
```
Bash Copy
```
```
nanonano .env.env
```
```
Bash Copy
```

### Congure access URLs

The next step is to con&gure the ROOT_URL and DOMAIN. These variables
determine how users will access your Rocket.Chat workspace. Con&guration
differs based on whether you are running a local test or a public production
instance.

## Local deployment

If you are only testing Rocket.Chat locally on your machine and do not require
external access or HTTPS, you can use the default values for these variables:

Your workspace will be accessible at [http://localhost:3000](http://localhost:3000) once succesfully
deployed.

## Production deployment with HTTPs

For a publicly accessible and secure deployment, con&gure HTTPS using a
domain name. Rocket.Chat's default con&guration leverages Trae&k, a reverse
proxy that can automatically manage and generate free TLS/SSL certi&cates
from Let's Encrypt.

Update the following variables in your .env &le:

```
RELEASE=7.5.0RELEASE=7.5.
```
```
Plaintext Copy
```
```
For production environments, it’s strongly recommended to specify a
&xed version number (e.g., 7.5.0) instead of using latest. This prevents
unexpected issues from automatic, breaking updates.
```
```
DOMAIN=localhostDOMAIN=localhost
ROOT_URL=http://localhostROOT_URL=http://localhost
```
```
Plaintext Copy
```
```
Before proceeding, verify that your domain's A record (or optional
CNAME record) is correctly pointed to the public IP address of the server
where you are running Docker.
```

```
Field Description Example
```
##### DOMAIN

```
Your public domain or subdomain
name. Do not include https:// or any
trailing slashes.
```
```
chat.example.com
```
##### ROOT_URL

```
The complete URL your users will
use to access the server, including
the secure protocol (https://).
```
```
https://chat.example.com
```
##### LETSENCR

##### YPT_ENAB

##### LED

```
Set to true to enable Trae&k to
automatically obtain and renew
certi&cates.
```
```
true
```
##### LETSENCR

##### YPT_EMAI

##### L

```
Your valid email address for
certi&cate renewal and security
noti&cations from Let's Encrypt.
```
```
demo@email.com
```
##### TRAEFIK_P

##### ROTOCOL

```
Set to https to ensure secure
connections are enforced by the
proxy.
```
```
https
```
Here is an example of the con&guration:

To use Nginx as a reverse proxy instead of Trae&k, see Con&gure Nginx as a
reverse proxy for Rocket.Chat.

### Enable monitoring

```
DOMAIN=chat.example.comDOMAIN=chat.example.com
ROOT_URL=https://chat.example.comROOT_URL=https://chat.example.com
```
```
LETSENCRYPT_ENABLED=trueLETSENCRYPT_ENABLED=true
LETSENCRYPT_EMAIL=demo@email.comLETSENCRYPT_EMAIL=demo@email.com
TRAEFIK_PROTOCOL=httpsTRAEFIK_PROTOCOL=https
```
```
Plaintext Copy
```

To effectively monitor your workspace, the rocketchat-compose repository
includes Prometheus for collecting metrics, Loki for log collection, and Grafana
for visualizing metrics and logs through dashboards. This setup allows you to
visualize key metrics, track performance, and gain insights into the health of
your Rocket.Chat instance.

You can choose to access Grafana either through a URL path (e.g.,
https://your-domain.com/grafana) or a subdomain (e.g.,
https://grafana.your-domain.com).

## Grafana via a path

This option is ideal for local development or if you prefer to keep all services
under a single domain, accessing your Grafana dashboard as a subdirectory
(e.g., https://your-domain.com/grafana).

To con&gure this, follow these steps in the .env &le:

```
1. You can use the default values for the following variables:
a. GRAFANA_DOMAIN: Leave this variable empty.
b. GRAFANA_PATH: Set this to your desired path, ensuring there is
no trailing slash (e.g., /grafana).
```
```
2. Set a strong password for the Grafana admin user by updating the
GRAFANA_ADMIN_PASSWORD variable:
```
## Grafana via a subdomain

Accessing your Grafana dashboard through a dedicated subdomain is the
recommended approach for production environments, as it provides a cleaner
and more secure access point.

##### GRAFANA_DOMAIN=GRAFANA_DOMAIN=

```
GRAFANA_PATH=/grafanaGRAFANA_PATH=/grafana
```
```
Plaintext Copy
```
```
GRAFANA_ADMIN_PASSWORD=your_secure_passwordGRAFANA_ADMIN_PASSWORD=your_secure_password
```
```
Plaintext Copy
```

To set this up, follow these steps:

```
1. Verify that your subdomain's A record is correctly pointed to the public IP
address of your server.
2. Open your .env &le and con&gure the following Grafana variables:
a. GRAFANA_DOMAIN: Set this to your desired subdomain for
accessing the Grafana dashboard. (e.g., grafana.your-
domain.com).
b. GRAFANA_PATH: Leave this variable empty.
```
```
3. Set a strong password for the Grafana admin user by updating the
GRAFANA_ADMIN_PASSWORD variable:
```
## Step 4: Optional congurations

Here are some additional optional con&gurations you can add to your .env &le
before launching Rocket.Chat:

```
1. External MongoDB connection: If you wish to use an existing or
dedicated MongoDB instance (such as a managed service like
MongoDB Atlas or a separately hosted deployment) instead of the
bundled local database, con&gure the MONGO_URL in the .env &le like
this:
```
```
GRAFANA_DOMAIN=grafana.your-domain.comGRAFANA_DOMAIN=grafana.your-domain.com
GRAFANA_PATH=GRAFANA_PATH=
```
```
Plaintext Copy
```
```
GRAFANA_ADMIN_PASSWORD=your_secure_passwordGRAFANA_ADMIN_PASSWORD=your_secure_password
```
```
Plaintext Copy
```
```
MONGO_URL=mongodb://<user>:MONGO_URL=mongodb://<user>:
<pass>@host1:27017,host2:27017,host3:27017/<databaseNam<pass>@host1:27017,host2:27017,host3:27017/<databaseNam
e>?replicaSet=<replicaSet>&ssl=true&authSource=admine>?replicaSet=<replicaSet>&ssl=true&authSource=admin
```
```
Plaintext Copy
```

```
2. Registration token: If you’ve received a registration token from our
Sales team or Rocket.Chat Cloud, you can use it to automatically
register your workspace upon initial startup. To do this, add the token to
the .env &le like this:
```
## Step 5: Launch the Rocket.Chat deployment

With your .env &le con&gured and saved, you're ready to start your

Rocket.Chat workspace.

```
1. Run the following command to download the all required Docker
images (Rocket.Chat, MongoDB, Trae&k, etc.) and start the containers in
the background:
```
```
Here’s a brief summary of what each &le in the command does:
a. compose.yml: Starts the Rocket.Chat application.
b. compose.database.yml: Manages MongoDB, the database that
Rocket.Chat relies on. It also includes NATS, an internal
message broker used for communication between services.
```
```
For production use, MongoDB must be deployed separately (non-
containerized) and con&gured as a replica set.
```
```
REG_TOKEN=your_token_hereREG_TOKEN=your_token_here
```
```
Plaintext Copy
```
```
This step is optional, as you can also register the workspace
through the setup wizard UI after deployment. Once the server
successfully starts and registration is complete, the token is
permanently stored in the database's cloud settings and is no
longer required in the .env &le. For security reasons, proceed to
remove the token from your .env &le after the &rst successful
deployment.
```
```
docker compose -f compose.database.yml -fdocker compose -f compose.database.yml -f
compose.monitoring.yml -f compose.traefik.yml -fcompose.monitoring.yml -f compose.traefik.yml -f
compose.yml up -dcompose.yml up -d
```
```
Bash Copy
```

```
c. compose.monitoring.yml: Enables monitoring with Prometheus
(for collecting metrics) and Grafana (for visualizing them in
dashboards).
d. compose.traefik.yml: Manages the Traek reverse proxy,
which handles secure routing and automatic HTTPS certi&cate
generation from Let's Encrypt.
You can easily customize your deployment by including only the services
you need. For example, if you did not con&gure monitoring or Trae&k
(e.g., you are using a separate Nginx proxy), you can omit those .yml
&les:
```
```
2. To check that all services have successfully started, use this command
to list all running containers:
```
If you encounter issues during deployment, refer to the following resources for
logging and troubleshooting:

```
Logging Rocket.Chat
Deployment FAQ
MongoDB best practices
```
## Step 6: Access your Rocket.Chat workspace

```
1. Once your Rocket.Chat instance is deployed, you can access the
workspace through your web browser to begin your &nal con&guration:
```
```
For local testing: Go to http://localhost:3000.
For production: Navigate to the ROOT_URL you con&gured in your .env
&le (e.g., https://your-domain.com).
```
```
2. After the workspace loads, follow the on-screen prompts to create your
&rst admin user and complete the initial workspace setup. Your
workspace and email will be registered to the Rocket.Chat Cloud portal
during this process.
```
```
docker compose -f compose.database.yml -f compose.ymldocker compose -f compose.database.yml -f compose.yml
up -dup -d
```
```
Bash Copy
```
```
docker docker psps
```
```
Bash Copy
```

### Access monitoring dashboard

You can access the Grafana dashboard at the path or subdomain you
con&gured in your .env &le (e.g., https://your-domain.com/grafana or
https://grafana.your-domain.com).

To log in to your Grafana dashboard, use the following credentials:

```
User: admin
Password: The password you set in the GRAFANA_ADMIN_PASSWORD
variable in your .env &le.
```
## Step 7: Update le storage

Rocket.Chat stores &le uploads using GridFS by default. While this doesn't
require extra setup, it's not ideal for production because it increases database
load and reduces scalability performance. Rocket.Chat highly recommends
using a dedicated object storage service such as Amazon S3, Google Cloud
Storage (GCS), or MinIO. Refer to the File Uploads guide for detailed
instructions on con&guring your preferred &le storage solution.

## Next steps

Great! You’ve successfully created your Rocket.Chat workspace with Docker and
logged in. Next, explore the following resources to continue using your new
workspace:

```
User Guides: Learn the basics of your Rocket.Chat account, the types of
rooms, and how to communicate with your workspace users.
Workspace Administration: Administrators and owners can set and
manage various con&gurations.
Marketplace: Explore the available apps to enhance your workspace.
```
## Update Rocket.Chat on Docker

```
The GRAFANA_ADMIN_PASSWORD you set in the .env &le is only
applied once during the &rst setup of the container. To update
your password later, you must change it directly within your
Grafana user preferences.
```

To update your Rocket.Chat version:

```
1. Modify the RELEASE variable in the .env &le to point to the release tag
of new version:
```
```
For example, RELEASE=8.0..
2. Run the following command to pull the new image and restart the
Rocket.Chat container:
```
```
3. After a few minutes, check that the new container is running:
```
```
You should see a new Rocket.Chat container running with the updated
version tag.
4. Return to your workspace Subscription to verify the updated Rocket.Chat
version.
```
## Update MongoDB on Docker

```
Updating the Rocket.Chat image will not affect your data, as it is stored
persistently in the separate MongoDB service. Before you proceed,
consult the general guidelines for updating MongoDB.
```
```
RELEASERELEASE==<<desired versiondesired version>>
```
```
Bash Copy
```
```
docker compose -f compose.yml up -ddocker compose -f compose.yml up -d
```
```
Bash Copy
```
```
Refer to the ofcial documentation for more details on
Rocket.Chat Docker images.
```
```
docker docker psps
```
```
Bash Copy
```
```
If you’re using the built in Bitnami MongoDB images, refer to this forum
post to upgrade to the of&cial MongoDB 8.2 image required for
Rocket.Chat 8.0.
```

Upgrading or downgrading your MongoDB instance may be required when
moving between major Rocket.Chat releases. Your Rocket.Chat workspace
stores its data in a dedicated MongoDB service. Updating the Rocket.Chat
container does not modify your stored data, as long as the MongoDB volume
remains intact.

This section explains how to safely change your MongoDB version for
Rocket.Chat in Docker, whether you are upgrading to a newer release or
downgrading to an earlier one.

## Upgrade MongoDB

If you are moving to a higher MongoDB version, follow the steps below:

```
1. Ensure that the MongoDB image for the target version is available on
Docker Hub under mongodb-community-server. Look for an image
tagged <version>-ubi.
2. Set or modify the MONGODB_VERSION variable in the .env &le to point to
your desired MongoDB version.
```
```
For example, MONGODB_VERSION=8..
3. Run the following command to pull the new image and restart the
MongoDB container:
```
```
4. After a few minutes, check that the new container is running:
```
```
Before you proceed, consult the general guidelines for updating
Rocket.Chat.
```
```
MONGODB_VERSIONMONGODB_VERSION==<<versionversion>>
```
```
Bash Copy
```
```
docker compose -f compose.database.yml up -ddocker compose -f compose.database.yml up -d
```
```
Bash Copy
```
```
Refer to the ofcial documentation for more details on
Rocket.Chat Docker images.
```
```
docker docker psps
```
```
Bash Copy
```

```
You should see a new MongoDB container running with the updated
version tag.
```
## Downgrade MongoDB

MongoDB does not support direct binary downgrades if the data &les were
created by a newer version. To safely downgrade, you must &rst lower the
Feature Compatibility Version (FCV) before changing the MongoDB image.
Failing to set the FCV before switching binaries before upgrading will cause
MongoDB to exit with an error and the container will repeatedly restart.

To downgrade your MongoDB version in Rocket.Chat, continue with these
steps:

```
1. Before making any changes, create a full database backup:
```
```
Replace <mongo-container_name> with the current name of the
MongoDB container.
2. Enter the MongoDB container:
```
```
3. Access the MongoDB shell:
```
```
4. Once in the shell, switch to the database:
```
```
Before you proceed, ensure that the MongoDB image for the targeted
version is available on Docker Hub under mongodb-community-server.
Look for an image tagged <version>-ubi.
```
```
docker docker execexec <<mongo-container_namemongo-container_name>> sh -c sh -c 'mongodump --'mongodump --
archive'archive' >> db.dumpdb.dump
```
```
Bash Copy
```
```
docker docker execexec -it -it <<mongo-container_namemongo-container_name>> bashbash
```
```
Bash Copy
```
```
mongoshmongosh
```
```
Bash Copy
```

5. Check the current Feature Compatibility Version:

```
The returned version should match your current MongoDB binary (for
example, 8.2).
```
6. Set the FCV to the version you intend to downgrade to:

7. Con&rm that the FCV now matches the target version:

8. Exit the mongo shell and shut down the MongoDB container:

9. Set or modify the MONGODB_VERSION variable in the .env &le to point to
your desired MongoDB version:

```
For example, MONGODB_VERSION=8..
```
```
use rocketchatuse rocketchat
```
```
Bash Copy
```
```
db.adminCommanddb.adminCommand(({{ getParameter: 1,getParameter: 1,
featureCompatibilityVersion: 1 featureCompatibilityVersion: 1 }}))
```
```
Bash Copy
```
```
db.adminCommanddb.adminCommand(({{
setFeatureCompatibilityVersion: setFeatureCompatibilityVersion: "<version>""<version>",,
confirm: confirm: truetrue
}}))
```
```
Bash Copy
```
```
db.adminCommanddb.adminCommand(({{ getParameter: 1,getParameter: 1,
featureCompatibilityVersion: 1 featureCompatibilityVersion: 1 }}))
```
```
Bash Copy
```
```
docker compose -f compose.database.yml downdocker compose -f compose.database.yml down
```
```
Bash Copy
```
```
MONGODB_VERSIONMONGODB_VERSION==<<versionversion>>
```
```
Bash Copy
```

```
10. Then, pull and start the database container:
```
```
11. After a few minutes, con&rm that the new MongoDB container is
running:
```
```
A new MongoDB container is now running with the updated version tag.
You can also double-check the MongoDb version in the container with
this command:
```
## Congure Nginx as a reverse proxy for

## Rocket.Chat on Docker

This section details how to replace the default Trae&k service with Nginx as a
reverse proxy to secure your Rocket.Chat deployment with HTTPS, using a free
TLS/SSL certi&cate from Let's Encrypt. This setup requires running Rocket.Chat
locally so Nginx can handle all public-facing secure traf&c.

### Prerequisites

Before starting, verify the following:

```
1. Your domain's A record (or optional CNAME record) is correctly pointed
to your server’s public IP address.
```
```
docker compose -f compose.database.yml up -ddocker compose -f compose.database.yml up -d
```
```
Bash Copy
```
```
docker docker psps
```
```
Bash Copy
```
```
docker docker execexec -it -it <<mongo-container_namemongo-container_name>> mongosh --evalmongosh --eval
"db.version()""db.version()"
```
```
Bash Copy
```
```
We recommend using Trae&k as it’s shipped with our of&cial Docker
image.
```

```
2. You have completed Step 1 and Step 2 to fetch the of&cial Rocket.Chat
Docker Compose con&guration &les.
```
#### Deploy Rocket.Chat locally

The next step is to con&gure Rocket.Chat to run on localhost so that Nginx can
act as the front-facing reverse proxy. Continue with these steps to run
Rocket.Chat on [http://localhost:3000:](http://localhost:3000:)

```
1. Verify the following variables in your .env &le are set to these default
values:
```
```
2. Modify the RELEASE variable in .env to specify the exact version of
Rocket.Chat you wish to deploy. Refer to the Rocket.Chat releases page
for a list of available stable versions.
```
```
3. Set a strong password for the Grafana admin user by updating the
GRAFANA_ADMIN_PASSWORD variable in .env:
```
```
DOMAIN=localhostDOMAIN=localhost
ROOT_URL=http://localhostROOT_URL=http://localhost
LETSENCRYPT_ENABLED=falseLETSENCRYPT_ENABLED=false
LETSENCRYPT_EMAIL=demo@email.comLETSENCRYPT_EMAIL=demo@email.com
TRAEFIK_PROTOCOL=httpTRAEFIK_PROTOCOL=http
GRAFANA_DOMAIN=GRAFANA_DOMAIN=
GRAFANA_PATH=/grafanaGRAFANA_PATH=/grafana
```
```
Plaintext Copy
```
##### RELEASE=7.5.0RELEASE=7.5.

```
Plaintext Copy
```
```
For production environments, it’s strongly recommended to
specify a &xed version number (e.g., 7.5.0) instead of using
latest. This prevents unexpected issues from automatic,
breaking updates.
```
```
GRAFANA_ADMIN_PASSWORD=your_secure_passwordGRAFANA_ADMIN_PASSWORD=your_secure_password
```
```
Plaintext Copy
```

```
4. Start your Rocket.Chat services, explicitly omitting the Trae&k
con&guration &le (compose.trae&k.yml):
```
```
5. Con&rm that all necessary services have started successfully:
```
Rocket.Chat should now be running locally on [http://localhost:3000.](http://localhost:3000.)

#### Get an SSL certicate from Let's Encrypt

Use Let's Encrypt to get a free & open-source SSL certi&cate by following these
steps:

```
1. Install certbot using the relevant package manager for your version of
Linux. For Debian-based distributions such as Debian and Ubuntu use :
```
```
For RPM-based distributions such as Redhat and Centos, use:
```
```
The installation commands may vary based on your Linux
distribution and version.
```
```
2. Obtain a certi&cate from Let's Encrypt by running this command (a
second or more domains are optional):
```
```
docker compose -f compose.database.yml -fdocker compose -f compose.database.yml -f
compose.monitoring.yml -f compose.yml up -dcompose.monitoring.yml -f compose.yml up -d
```
```
Bash Copy
```
```
docker docker psps
```
```
Bash Copy
```
```
sudosudo aptapt updateupdate
sudosudo aptapt installinstall certbotcertbot
```
```
Bash Copy
```
```
sudosudo yum yum installinstall yum-utilsyum-utils
sudosudo yum yum installinstall nginxnginx
```
```
Bash Copy
```

```
Replace <emailaddress@email.com> and <domain.com> with your
actual email adress and domain names. If successful, your certi&cate
&les will be stored in /etc/letsencrypt/live/<your.domain.com>/.
```
#### Congure Nginx web server with TLS/SSL

Now, link your domain to the Rocket.Chat service running locally on port 3000.
Nginx will serve as the secure public entry point, handling HTTPS traf&c on port
443 and directing requests to the local Rocket.Chat and Grafana services.

```
1. Install Nginx web server:
```
```
2. Backup the default con&g &le for reference:
```
```
3. Create a new site con&guration for Rocket.Chat:
```
```
4. Paste the following in the new &le:
```
```
sudosudo certbot certonly --standalone --emailcertbot certonly --standalone --email
<<emailaddress@email.comemailaddress@email.com>> -d -d <<domain.comdomain.com>> -d-d
<<subdomain.domain.comsubdomain.domain.com>>
```
```
Bash Copy
```
```
sudosudo apt-getapt-get installinstall nginxnginx
```
```
Bash Copy
```
```
cdcd /etc/nginx/sites-available/etc/nginx/sites-available
sudosudo mvmv default default.referencedefault default.reference
```
```
Bash Copy
```
```
sudosudo nanonano /etc/nginx/sites-available/default/etc/nginx/sites-available/default
```
```
Bash Copy
```
```
server {server {
listen 443 ssl;listen 443 ssl;
server_name <ABC.DOMAIN.COM>; #replaceserver_name <ABC.DOMAIN.COM>; #replace
<ABC.DOMAIN.COM> with your domain name<ABC.DOMAIN.COM> with your domain name
ssl_certificatessl_certificate
```
```
Plaintext Copy
```

/etc/letsencrypt/live/<ABC.DOMAIN.COM>/fullchain.pem;/etc/letsencrypt/live/<ABC.DOMAIN.COM>/fullchain.pem;

#replace <ABC.DOMAIN.COM> with your domain name#replace <ABC.DOMAIN.COM> with your domain name

ssl_certificate_keyssl_certificate_key

/etc/letsencrypt/live/<ABC.DOMAIN.COM>/privkey.pem;/etc/letsencrypt/live/<ABC.DOMAIN.COM>/privkey.pem;

#replace <ABC.DOMAIN.COM> with your domain name#replace <ABC.DOMAIN.COM> with your domain name

ssl_protocols TLSv1 TLSv1.1 TLSv1.2;ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

ssl_prefer_server_ciphers on;ssl_prefer_server_ciphers on;

ssl_ciphersssl_ciphers

'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';

root /usr/share/nginx/html;root /usr/share/nginx/html;

index index.html index.htm;index index.html index.htm;

# Make site accessible from [http://localhost/#](http://localhost/#) Make site accessible from [http://localhost/](http://localhost/)

server_name localhost;server_name localhost;

location / {location / {

proxy_pass [http://localhost:3000/;proxy_pass](http://localhost:3000/;proxy_pass) [http://localhost:3000/;](http://localhost:3000/;)

proxy_http_version 1.1;proxy_http_version 1.1;

proxy_set_header Upgrade $http_upgrade;proxy_set_header Upgrade $http_upgrade;

proxy_set_header Connection "upgrade";proxy_set_header Connection "upgrade";

proxy_set_header Host $http_host;proxy_set_header Host $http_host;

proxy_set_header X-Real-IP $remote_addr;proxy_set_header X-Real-IP $remote_addr;

proxy_set_header X-Forwarded-Forproxy_set_header X-Forwarded-For

$proxy_add_x_forwarded_for;$proxy_add_x_forwarded_for;

proxy_set_header X-Forwarded-Proto http;proxy_set_header X-Forwarded-Proto http;

proxy_set_header X-Nginx-Proxy true;proxy_set_header X-Nginx-Proxy true;

proxy_redirect off;proxy_redirect off;

}}

# Grafana Reverse Proxy # Grafana Reverse Proxy

location /grafana {location /grafana {

proxy_pass [http://localhost:5050/grafana;proxy_pass](http://localhost:5050/grafana;proxy_pass) [http://localhost:5050/grafana;](http://localhost:5050/grafana;)

proxy_http_version 1.1;proxy_http_version 1.1;

proxy_set_header Upgrade $http_upgrade;proxy_set_header Upgrade $http_upgrade;

proxy_set_header Connection "upgrade";proxy_set_header Connection "upgrade";

proxy_set_header Host $http_host;proxy_set_header Host $http_host;

proxy_set_header X-Real-IP $remote_addr;proxy_set_header X-Real-IP $remote_addr;

proxy_set_header X-Forwarded-Forproxy_set_header X-Forwarded-For

$proxy_add_x_forwarded_for;$proxy_add_x_forwarded_for;

proxy_set_header X-Forwarded-Proto https; #proxy_set_header X-Forwarded-Proto https; #


```
Replace ABC.DOMAIN.COM with your domain name.
```
```
5. Test the Nginx con&guration to make sure there are no syntax errors:
```
```
6. If the syntax test is successful, restart Nginx:
```
#### Access your workspace

```
1. Once your Rocket.Chat instance is deployed, you can access the
workspace workspace securely at https://<your-domain.com>.
```
```
2. After the workspace loads, follow the on-screen prompts to create your
&rst admin user and complete the initial workspace setup. Your
workspace and email will be registered to the Rocket.Chat Cloud portal
during this process.
```
#### Access monitoring dashboard

```
Crucial for secure path accessCrucial for secure path access
proxy_set_header X-Nginx-Proxy true;proxy_set_header X-Nginx-Proxy true;
proxy_redirect off;proxy_redirect off;
} }
}}
server {server {
listen 80;listen 80;
server_name <ABC.DOMAIN.COM>; #replaceserver_name <ABC.DOMAIN.COM>; #replace
<ABC.DOMAIN.COM> with your domain name<ABC.DOMAIN.COM> with your domain name
return 301 https://$host$request_uri;return 301 https://$host$request_uri;
}}
```
```
sudosudo nginx -tnginx -t
```
```
Bash Copy
```
```
sudosudo systemctl restart nginxsystemctl restart nginx
```
```
Bash Copy
```
```
If you have security group restrictions, allow TCP/22 from your
current IP for SSH connections and TCP/443 from the IP you plan
to use for access.
```

You can access the Grafana dashboard at the path or subdomain you
con&gured in your .env &le (e.g., https://your-domain.com/grafana or

```
https://grafana.your-domain.com).
```
To log in to your Grafana dashboard, use the following credentials:

```
User: admin
Password: The password you set in the GRAFANA_ADMIN_PASSWORD
variable in your .env &le.
```
Great job! You have successfully enabled TLS on your Rocket.Chat workspace
with Nginx.

## Set Rocket.Chat deployment environment

## variable on Docker

Enviroment variables are additional settings that impacts your workspace
deployment and con&guration. Refer to the of&cial documentation to learn more
about Rocket.Chat deployment enviroment variables.

To set up an environment variable in Docker,

```
1. Open your compose.yml &le:
```
```
2. Add the environment variable under the environment section of the
rocketchat service. For example, to override the SMTP Host setting,
add:
```
```
The GRAFANA_ADMIN_PASSWORD you set in the .env &le is only
applied once during the &rst setup of the container. To update
your password later, you must change it directly within your
Grafana user preferences.
```
```
nanonano compose.ymlcompose.yml
```
```
Bash Copy
```

```
3. Restart your Rocket.Chat container to apply the changes:
```
For a full list of available environment variables, refer to Deployment
Environment Variables.

## Additional steps

### Rocket.Chat Docker images

Rocket.Chat provides an of&cial Docker image repository maintained by the
Rocket.Chat team. When setting up your deployment, you specify which
Rocket.Chat version to use by adding the image tag.

The base image path is: registry.rocket.chat/rocketchat/rocket.chat:
<tag>.

Here are the available image tags:

```
servicesservices::
rocketchatrocketchat::
......
environmentenvironment::
......
OVERWRITE_SETTING_SMTP_HostOVERWRITE_SETTING_SMTP_Host:: "my.smtp.server.com""my.smtp.server.com"
```
```
YAML Copy
```
```
Tip: This is the same section where ROOT_URL, MONGO_URL, etc
is de&ned.
```
```
docker compose stop rocketchatdocker compose stop rocketchat
docker compose docker compose rmrm rocketchatrocketchat
docker compose up -d rocketchatdocker compose up -d rocketchat
```
```
Bash Copy
```

```
1. Specic release tags (recommended for production): You can lock your
workspace to a speci&c Rocket.Chat version using a &xed version tag.
This is the best practice for production environments because it prevents
unexpected major updates and ensures you only upgrade when you are
ready to perform backups and check database compatibility. For
example, registry.rocket.chat/rocketchat/rocket.chat:6.6.0.
Check out the release notes or Docker hub tags for the various
Rocket.Chat releases.
2. Latest tag: The latest tag points to the most recent Rocket.Chat build
available in the registry. This may include recent changes and is useful
for testing newer versions. Using this tag for production deployments is
not recommended.
3. Develop build: The develop tag tracks the develop branch and
includes the newest, untested changes. This tag is intended for
development and experimentation only and should not be used in
production.
```
### Back up and restore MongoDB data on Docker

Rocket.Chat stores all workspace data in MongoDB. Follow the steps in the
commands below to back up and restore your database when running in
Docker.

#### Back up your MongoDB database on Docker

```
1. List all running Docker containers to &nd the name of your MongoDB
service
```
```
2. Run this command to dump the database into a binary &le db.dump :
```
```
When successful, you should see db.dump &le in the current directory.
```
```
docker docker psps -a-a
```
```
Bash Copy
```
```
Take note of your mongo container name (e.g., rocketchat-
mongodb-1 )
```
```
docker docker execexec <<container_namecontainer_name>> sh -c sh -c 'mongodump --'mongodump --
archive'archive' >> db.dumpdb.dump
```
```
Bash Copy
```

#### Restore MongoDB database backup on Docker

To restore the data, use mongorestore inside the MongoDB container, piping
the data from your local db.dump &le.

#### Export to an external database (e.g., MongoDB Atlas)

If you are migrating your data or performing an ofine restore, you can use the
local db.dump &le directly with an external MongoDB connection string.

### Monitoring and logging your deployment

Effective logging helps you monitor the health and status of your Rocket.Chat
deployment. This section covers logging for Rocket.Chat, MongoDB, and
reverse proxies like Trae&k or Nginx.

To check the status and logs of your containers, follow these steps:

```
1. Use one of the following commands to view containers in your Docker
environment:
```
```
These commands help you identify the containers running Rocket.Chat,
MongoDB, Trae&k, and related services.
2. Once you identify the relevant container or service, use one of the
following commands to inspect logs:
```
```
docker docker execexec -i -i <<container_namecontainer_name>> sh -c sh -c 'mongorestore --archive''mongorestore --archive' <<
db.dumpdb.dump
```
```
Bash Copy
```
```
mongorestore --uri mongodb+srv://mongorestore --uri mongodb+srv://<<useruser>>::
<<passwordpassword>>@cluster0.w2btl.mongodb.net --archive@cluster0.w2btl.mongodb.net --archive==db.dumpdb.dump
```
```
Bash Copy
```
```
docker docker psps # Shows currently running containers# Shows currently running containers
with details like ID, status, and ports with details like ID, status, and ports
docker docker psps -a -a # Lists all containers, including# Lists all containers, including
stopped ones stopped ones
docker compose docker compose psps # Lists services managed by Docker# Lists services managed by Docker
ComposeCompose
```
```
Bash Copy
```

```
3. To inspect MongoDB’s health, run:
```
```
This command returns detailed MongoDB server status information.
```
```
4. If using Nginx as a reverse proxy, logs are stored inside the container at
/var/log/nginx/. To view logs, run:
```
Encountering issues with your deployment? See the Deployment FAQ for
detailed troubleshooting assistance.

```
docker compose logs -f docker compose logs -f <<service_nameservice_name>> # Shows realtime# Shows realtime
logs for a service managed by Docker Compose logs for a service managed by Docker Compose
docker logs docker logs <<container_namecontainer_name>> # Displays logs for# Displays logs for
the specific container the specific container
docker logs -f docker logs -f <<container_namecontainer_name>> # Shows real-time# Shows real-time
logs for a containerlogs for a container
docker logs --tail 10 docker logs --tail 10 <<container_namecontainer_name>> # Displays the# Displays the
last 10 lines of logslast 10 lines of logs
```
```
Bash Copy
```
```
Replace <container_name> and <service_name> with
the appropriate values.
Rocket.Chat logs to stdout, so there isn't a speci&c log &le
within the container. The Docker logging system captures
these stdout logs, making them accessible through the
docker logs command
```
```
docker docker execexec -it -it <<mongodb_container_namemongodb_container_name>> mongosh --evalmongosh --eval
"db.runCommand({ serverStatus: 1 })""db.runCommand({ serverStatus: 1 })"
```
```
Bash Copy
```
```
Replace <mongodb_container_name> with your MongoDB
container name.
```
```
sudosudo tailtail -f /var/log/nginx/access.log -f /var/log/nginx/access.log # Access logs # Access logs
sudosudo tailtail -f /var/log/nginx/error.log -f /var/log/nginx/error.log # Error logs# Error logs
```
```
Bash Copy
```

Congratulations on successfully deploying Rocket.Chat using Docker! You can
now effortlessly communicate with your team members in your workspace. Visit
the accessing your workspace guide to con&gure your workspace and onboard
other team members.
