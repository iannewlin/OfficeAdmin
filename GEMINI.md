# OfficeAdmin Project Documentation

## Mission Statement
To establish a secure, self-sovereign suite of productivity tools tailored for a mental health practice. This stack aims to replace privacy-invasive or expensive public SaaS offerings (Gmail, Dropbox, Slack, 1Password) with HIPAA-compliant*, self-hosted alternatives that ensure client data privacy, data ownership, and operational resilience.

*Note: While the software is capable of HIPAA compliance, actual compliance depends on proper configuration, encryption, and BAA (if using external storage/relays).*

## Theme: Athena
Named after **Athena**, the goddess of wisdom, strategy, and handicraft.
*   **Role:** The organizer and strategist.
*   **Function:** Provides the tools and structure necessary for the practice to operate efficiently and intelligently.

## Strategic Roadmap

### Phase 1: The Secure Vault (Passwords & Docs)
*   **Objective:** Securely store sensitive credentials and administrative documents.
*   **Core Services:**
    *   **Vaultwarden (Bitwarden):** Self-hosted password manager for sharing credentials safely within the practice.
    *   **Nextcloud:** Cloud storage for administrative files (policies, forms, non-PHI business docs) replacing Dropbox/Google Drive.

### Phase 2: The Communication Hub (Chat & Mail)
*   **Objective:** Streamline internal communication and manage external correspondence.
*   **Core Services:**
    *   **Mattermost:** A secure, self-hosted Slack alternative for real-time team collaboration, case consultation (de-identified), and operational announcements.
    *   **Mail Server (e.g., Poste.io or Stalwart):** Self-hosted email solution to own the domain's communications, ensuring no third-party scanning of sensitive correspondence.

### Phase 3: The Integrated Office (Workflows)
*   **Objective:** Connect these isolated tools into a cohesive operating system.
*   **Features:**
    *   **n8n Automation:** Deployed instance on port 5679 to handle business operations, finances, and workflow automation.
    *   **Baserow Database:** No-code database platform on port 8081 for managing practice data (finances, inventory, client tracking).
    *   **Single Sign-On (SSO):** Unify access using a central identity provider (possibly Authentik or Keycloak, or leveraging Tailscale).

## Proposed Tech Stack (Docker-Based)

### 1. Cloud Storage & Collaboration
*   **Service:** **Nextcloud AIO** (All-in-One) or **Nextcloud** standalone.
*   **Role:** File storage, Calendars (CalDAV), Contacts (CardDAV).
*   **Why:** Industry standard for open-source self-hosted cloud. Robust mobile apps.

### 2. Team Communication
*   **Service:** **Mattermost**.
*   **Role:** Internal chat, channel-based organization.
*   **Why:** "Slack-compatible" user experience, high security, good mobile app.

### 3. Password Management
*   **Service:** **Vaultwarden**.
*   **Role:** Encrypted credential storage.
*   **Why:** Lightweight Rust implementation of Bitwarden. Compatible with official Bitwarden clients.

### 4. Email Services
*   **Service:** **Poste.io** or **Stalwart Mail Server**.
*   **Role:** SMTP/IMAP server, Webmail.
*   **Why:** 
    *   *Poste.io:* Simple, monolithic container, easy to manage.
    *   *Stalwart:* Modern, written in Rust, highly performant and secure.

### 5. Workflow Automation
*   **Service:** **n8n**.
*   **Role:** Visual automation tool for business logic and integrations.
*   **Port:** 5679 (to avoid conflict with Hermes).
*   **URL:** `https://fedora.bream-kettle.ts.net:5679/`

### 6. Database Management
*   **Service:** **Baserow**.
*   **Role:** No-code database platform for structured data management.
*   **Port:** 8081.
*   **URL:** `https://fedora.bream-kettle.ts.net:8081/`

## Integration with Ecosystem
*   **Host:** `Heimdall` (100.91.66.125) - The central server.
*   **Network:** **Tailscale** for secure remote access without exposing ports to the public internet (except potentially port 25 for Email).
*   **Storage:** Data will reside on `Heimdall`, likely backed up to the ZFS array or similar reliable storage.
*   **Monitoring:** **SysAdminStack** (Uptime Kuma) will monitor service health.

## Security Considerations
*   **Data at Rest:** All volumes (especially Nextcloud and Mail) must be on encrypted disks or encrypted at the application level.
*   **Access Control:** Strict ACLs via Tailscale. Only authorized devices can access the admin interfaces.
*   **Backups:** Essential. Nextcloud and Mail data are critical business assets.
