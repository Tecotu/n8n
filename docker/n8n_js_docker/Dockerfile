# Ubuntu 22.04 (Jammy) + Node 22 + n8n + Playwright (Chromium)
FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive NODE_ENV=production

# 1) Basis-Tools
RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates curl gnupg wget dumb-init \
 && rm -rf /var/lib/apt/lists/*

# 2) Node 22 aus NodeSource
RUN curl -fsSL https://deb.nodesource.com/setup_22.x | bash - \
 && apt-get update && apt-get install -y --no-install-recommends nodejs \
 && rm -rf /var/lib/apt/lists/*

# 3) n8n + Playwright global installieren
RUN npm config set update-notifier false \
 && npm install -g --no-audit --no-fund n8n@latest playwright@latest

# 4) Playwright-Browser inkl. System-Dependencies für Ubuntu holen
#    Wir legen Browser unter /ms-playwright ab (lesbar für alle User)
ENV PLAYWRIGHT_BROWSERS_PATH=/ms-playwright
RUN npx playwright install --with-deps chromium \
 && chmod -R 755 /ms-playwright

# 5) Runtime-User
RUN useradd -m -u 1000 n8nuser
USER n8nuser
WORKDIR /home/n8nuser

# 6) n8n-Umgebung
ENV N8N_HOST=0.0.0.0 \
    N8N_PORT=5678 \
    N8N_PROTOCOL=http \
    TZ=Europe/Berlin \
    NODE_FUNCTION_ALLOW_EXTERNAL=playwright \
    NODE_PATH=/usr/local/lib/node_modules \
    N8N_ENFORCE_SETTINGS_FILE_PERMISSIONS=true

VOLUME ["/home/n8nuser/.n8n"]
EXPOSE 5678

ENTRYPOINT ["dumb-init","--"]
CMD ["n8n","start"]
