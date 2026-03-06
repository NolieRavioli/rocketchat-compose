# Rocket.Chat Compose Files

 Compose files for running Rocket.Chat with Traefik, MongoDB, NATS, and optional monitoring.

## Important Note!

 If you are using the built in MongoDB and are still using the Bitnami MongoDB Images please see this important forum post on how to update:

 https://forums.rocket.chat/t/action-required-docker-compose-moving-from-bitnami-to-official-mongodb-image/22693

## TLDR;

 1. Install docker
 2. Clone: `git clone https://github.com/NolieRavioli/rocketchat-compose.git`
 3. cd to the cloned dir: `cd rocketchat-compose`
 4. Copy the example environment file: `cp .env.example .env`
 5. Edit `nano .env` file and update values
 6. Start the stack: `docker compose -f compose.database.yml -f compose.traefik.yml -f compose.yml up -d --force-recreate`

 You can access Rocket.Chat at: https://localhost

 You can login to Grafana at: https://localhost/grafana with the default credentials:

   - User: admin
   - Password: pass

## Getting Started

 Install Docker and supporting tools:

   ```bash
   sudo apt install -y curl git && \
   sudo curl -L https://get.docker.com | sudo sh && \
   sudo usermod -aG docker $USER
   ```

 Reboot so your user gets Docker group permissions:

   ```bash
   sudo reboot
   ```

### Clone

 ```bash
 git clone https://github.com/NolieRavioli/rocketchat-compose.git && \
 cd rocketchat-compose
 ```

---

### Docker/Podman Compose

 When deploying with Podman, it is required to enable the user-level Podman socket with `systemctl --user enable --now podman.socket`. Note that `sudo` is not used, as it should be enabled at the user level.

 For deploying the recommended stack with Rocket.Chat, Traefik, MongoDB, NATS, and Prometheus for monitoring:

 1. **Configure environment variables:**
    - Copy the example environment file:
      ```bash
      cp .env.example .env
      ```
    - Edit `.env` to fit your deployment. Recommended changes - we recommend to keep other values from the example for reference.
      ```env
      # Rocket.Chat Cloud registration token (optional)
      REG_TOKEN=
      # Set to 'https' to enable HTTPS with Traefik (recommended for internet exposure)
      TRAEFIK_PROTOCOL=https
      # Set to true after you've set your domain and lets encrypt email
      LETSENCRYPT_ENABLED=
      # Email for Let's Encrypt certificate
      LETSENCRYPT_EMAIL=
      # Domain for Rocket.Chat
      DOMAIN=localhost
      # Domain for Grafana, blank to use as a path
      GRAFANA_DOMAIN=
      GRAFANA_PATH=/grafana
      # Should match your domain; use https if enabled
      ROOT_URL=https://localhost
      ```

 2. **Using Grafana as a Subdomain instead of Path:**

    - Grafana is `https://<DOMAIN>/grafana` by default:

      ```env
      # set this to empty
      GRAFANA_DOMAIN=
      # set this to you desired path without trailing slash
      GRAFANA_PATH=/grafana
      ```

    - If you want to use subdomain

      ```env
      # set this to your subdomain
      GRAFANA_DOMAIN=grafana.your-domain.com
      # set this as empty
      GRAFANA_PATH=
      ```

 3. **Start the stack:**
    - With Docker Compose:
      ```bash
      docker compose \
        -f compose.monitoring.yml \
        -f compose.traefik.yml \
        -f compose.database.yml \
        -f compose.yml \
        -f docker.yml \
        up -d
      ```
    - Or with Podman Compose (rootless, recommended):
      ```bash
      podman compose \
        -f compose.monitoring.yml \
        -f compose.traefik.yml \
        -f compose.database.yml \
        -f compose.yml \
        -f podman.yml \
        up -d
      ```
    - If your Podman setup requires rootful mode, use:
      ```bash
      podman compose \
        -f compose.monitoring.yml \
        -f compose.traefik.yml \
        -f compose.database.yml \
        -f compose.yml \
        -f podman-rootful.yml \
        up -d
      ```

    This will launch all containers. Rocket.Chat will be available at [http://localhost](http://localhost), and Grafana at [http://grafana.localhost](http://grafana.localhost).

    > **Note:** Rootless Podman is preferred for security and compatibility. Use rootful mode only if required by your environment. If deploying to a custom domain, update `ROOT_URL` and related variables accordingly.

 4. **Stop the stack:**

    - With Docker Compose:
      ```bash
      docker compose -f docker.yml --profile '*' down
      ```
    - Or with Podman Compose:
      ```bash
      podman compose \
        -f compose.monitoring.yml \
        -f compose.traefik.yml \
        -f compose.database.yml \
        -f compose.yml \
        -f podman.yml \
        down
      ```

---

### Customizing the stack

 To exclude components (e.g., MongoDB or Prometheus), simply remove their compose files from the command. For example, to deploy Rocket.Chat with Traefik only:

 ```bash
 podman compose \
   -f compose.traefik.yml \
   -f compose.yml \
   up -d
 ```

---

### Multiple servers

 When running multiple Rocket.Chat servers, you can configure Traefik to discover those servers and include them in load balancing by adding a variable in the `.env` file:

 ```env
 ROCKETCHAT_BACKEND_SERVERS=rocketchat-1:3000,rocketchat-2:3000,rocketchat-3:3000
 ```