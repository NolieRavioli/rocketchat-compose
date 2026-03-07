# Rocket.Chat Compose Files

 Compose files for running Rocket.Chat with Traefik, MongoDB, NATS, optional Jitsi, and optional monitoring.

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
 7. If you want Jitsi, set `ENABLE_JITSI=true` in `.env` and add `-f compose.jitsi.yml`

 You can access Rocket.Chat at: https://localhost

 You can login to Grafana at: https://localhost/grafana with the default credentials:

   - User: admin
   - Password: pass

 If Jitsi is enabled, it will be available at: https://meet.localhost

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
      # Enable Jitsi only when you include compose.jitsi.yml
      ENABLE_JITSI=false
      # Domain and public URL for Jitsi
      JITSI_DOMAIN=meet.localhost
      JITSI_PUBLIC_URL=https://meet.localhost
      # Required for Jitsi media. Use the IP clients reach for this host.
      JITSI_JVB_ADVERTISE_IPS=
      # Required service passwords for Jitsi
      JITSI_JICOFO_AUTH_PASSWORD=
      JITSI_JVB_AUTH_PASSWORD=
      # Domain for Grafana, blank to use as a path
      GRAFANA_DOMAIN=
      GRAFANA_PATH=/grafana
      # Should match your domain; use https if enabled
      ROOT_URL=https://localhost
      # SMTP Configuration
      # Leave blank to skip SMTP configuration
      SMTP_HOST=
      SMTP_PORT=587
      SMTP_USERNAME=
      SMTP_PASSWORD=
      SMTP_FROM_EMAIL=noreply@localhost
      # Protocol: smtp or smtps
      SMTP_PROTOCOL=smtp
      # Set to true to ignore TLS/SSL errors (not recommended for production)
      SMTP_IGNORE_TLS=false
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
    - To include Jitsi, add `-f compose.jitsi.yml` before `-f compose.yml`:
      ```bash
      docker compose \
        -f compose.monitoring.yml \
        -f compose.traefik.yml \
        -f compose.database.yml \
        -f compose.jitsi.yml \
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
    - To include Jitsi with Podman, add `-f compose.jitsi.yml` before `-f compose.yml`.
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
    - To include Jitsi with rootful Podman, add `-f compose.jitsi.yml` before `-f compose.yml`.

    This will launch all containers. Rocket.Chat will be available at [https://localhost](https://localhost), Grafana at [https://localhost/grafana](https://localhost/grafana), and Jitsi at [https://meet.localhost](https://meet.localhost) when enabled.

    > **Note:** Rootless Podman is preferred for security and compatibility. Use rootful mode only if required by your environment. If deploying to a custom domain, update `ROOT_URL` and related variables accordingly.

 4. **Stop the stack:**

    - With Docker Compose:
      ```bash
      docker compose \
        -f compose.monitoring.yml \
        -f compose.traefik.yml \
        -f compose.database.yml \
        -f compose.yml \
        -f docker.yml \
        down
      ```
    - If you started Jitsi, include `-f compose.jitsi.yml` in the same command.
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
    - If you started Jitsi with Podman, include `-f compose.jitsi.yml` in the same command.

---

### Jitsi setup

 To add Jitsi to the stack:

 1. Set `ENABLE_JITSI=true` in `.env`.
 2. Set `JITSI_DOMAIN` and `JITSI_PUBLIC_URL` to the hostname clients will use for Jitsi.
 3. Set `JITSI_JVB_ADVERTISE_IPS` to the IP address clients use to reach the Docker host.
 4. Set strong unique values for `JITSI_JICOFO_AUTH_PASSWORD` and `JITSI_JVB_AUTH_PASSWORD`.
 5. Start the stack with `-f compose.jitsi.yml`.

 Important notes:

 - `localhost` only works for single-machine testing. If other users will join from the LAN, use real DNS or host-file entries for both `DOMAIN` and `JITSI_DOMAIN`.
 - Jitsi requires UDP `10000` to reach the host for media.
 - This repo runs Jitsi behind Traefik on a dedicated hostname. Do not try to serve Jitsi from a subpath.

 To finish the Rocket.Chat integration:

 1. Install the **Jitsi** app from the Rocket.Chat Marketplace.
 2. In the app settings, set **Domain** to your `JITSI_DOMAIN`.
 3. Enable **Use SSL** when `JITSI_PUBLIC_URL` uses `https`.
 4. Leave JWT disabled unless you are intentionally configuring token auth on both sides.

 The default Jitsi image pin is `stable-10741`, which satisfies Rocket.Chat desktop support requirements for Jitsi `2.0.10073` or newer.

---

### Generated AIO files

 The `generated/` directory contains pre-merged compose bundles:

 - `generated/docker-compose.yml`: Rocket.Chat + MongoDB + Traefik
 - `generated/docker-compose-jitsi.yml`: Rocket.Chat + MongoDB + Traefik + Jitsi
 - `generated/docker-compose-no-traefik.yml`: Rocket.Chat + MongoDB

 Example:

 ```bash
 docker compose -f generated/docker-compose-jitsi.yml up -d
 ```

 These generated bundles do not include the monitoring overlay.

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
