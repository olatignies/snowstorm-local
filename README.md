# Snowstorm-local

This document serves as a user guide for launching the Snowstorm terminology server, customized by E-health.
See the forked repository here: [https://github.com/ehealthplatformstandards/snowstorm](https://github.com/ehealthplatformstandards/snowstorm).

Snowstorm supports multiple terminologies, as described in \[**syndication-terminologies.md**], and offers flexible control over which terminologies are loaded at startup (see \[**syndication-on-startup.md**]).
Some terminologies can also be updated at runtime—details can be found in \[**syndication-on-runtime.md**].

This guide outlines the minimal steps required to launch the server using the default configuration.

---

## 🚀 Launch Instructions

If the `docker-compose.yml` file is left unchanged, the following terminologies will be automatically loaded at startup:

* **ATC**, **BCP13**, **BCP47**, **ISO3166**, **ISO3166-2**, **UCUM**

These terminologies are pre-packaged within the Docker image and require only an import.

In addition, the latest versions of **LOINC**, **HL7**, and **SNOMED CT** (both the Belgian extension and the international edition) will be **downloaded and imported at runtime**.
Other terminology versions can be configured through the docker-compose file (e.g. a specific **SNOMED CT** German extension).

🕒 **Note**: The initial startup may take up to **40 minutes**, depending on network speed and system performance.

---

## ⚙️ Step 1: Configure the `.env` File

1. Set a **custom secret** for the `SYNDICATION_SECRET` environment variable.
   This is required to enable terminology updates at runtime.

2. If you plan to import **SNOMED CT** and **LOINC**, make sure to add your **personal credentials** (e.g., for the respective licensing portals) to the `.env` file.

---

## ▶️ Step 2: Start the Server

To start the terminology server (along with **Elasticsearch** and the **SNOMED CT Browser**), run:

```bash
docker compose up
```

---

## 📌 Note

By using Snowstorm, you implicitly agree to the **terms and conditions** associated with each terminology you import or use.