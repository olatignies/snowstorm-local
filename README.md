# Snowstorm-Local

This document is a user guide for launching the **Snowstorm terminology server**, customized by **E-health**.
The forked repository is available here: [https://github.com/ehealthplatformstandards/snowstorm](https://github.com/ehealthplatformstandards/snowstorm).

Snowstorm supports multiple terminologies (see \[**syndication-terminologies.md**]) and offers flexible control over which ones are loaded at startup (see \[**syndication-on-startup.md**]).
Some terminologies can also be updated during runtime—see \[**syndication-on-runtime.md**] for more details.

This guide describes the minimal steps required to launch the server using the default setup.

---

## 🚀 Quick Launch Overview

If the provided `docker-compose.yml` file is used without modification, the following terminologies will be imported at startup:

* **ATC**, **BCP13**, **BCP47**, **ISO3166**, **ISO3166-2**, **UCUM**

These terminologies are embedded in the Docker image and require only import, not download.

In addition, the most recent versions of **LOINC**, **HL7**, and **SNOMED CT** (including the Belgian extension and international edition) will be **downloaded and imported at runtime**.

Other versions or terminology sets can be configured via application launch arguments (e.g. to load the SNOMED CT German extension).

This guide assumes usage of `docker-compose`, but you are free to run Snowstorm in other environments (e.g. Docker Swarm, Kubernetes, etc.). 
In both cases, ElasticSearch must be up and running and configured to work with Snowstorm. Snowstorm will fail to properly start, if ElasticSearch is not running/configured correctly.

🕒 **Note**: The initial startup may take up to **40 minutes**, depending on internet speed and system performance.

---

## ⚙️ Step 1: Configure the `.env` File

1. Set a **custom secret** for the `SYNDICATION_SECRET` environment variable.
   This is required to enable terminology updates during runtime.

2. To import **SNOMED CT** and **LOINC**, add your personal credentials (e.g., for the relevant licensing portals) to the `.env` file.

---

## ⚙️ Step 2 (Optional): Customize Launch Configuration

The `docker-compose.yml` file contains a `command` section that controls what happens during container startup.

Key notes:

* Remove or comment out the `--syndication` argument if you do not want to import any terminology at startup.
* Remove individual flags like `--hl7`, `--loinc`, or `--snomed` if you want to skip those specific imports.
* You can specify custom versions for terminologies.

**Example**:

```yaml
command: [
  "--elasticsearch.urls=http://es:9200",
  "--syndication",
  "--hl7",
  #"--hl7=6.1.0",
  #"--loinc",
  #"--loinc=2.78",
  #"--snomed=http://snomed.info/sct/11000172109",
  "--snomed=http://snomed.info/sct/11000172109/version/20250315",
  "--extension-country-code=BE"
]
```

This configuration loads:

* The latest **HL7** version
* A specific version of the **SNOMED CT Belgian extension**
* Other terminologies (**ATC**, **BCP13**, ... )enabled by the `--syndication` flag

LOINC will **not** be imported in this example setup.

---

## ▶️ Step 3: Start the Server

To start the terminology server (including **Elasticsearch** and the **SNOMED CT Browser**), run:

```bash
docker compose up
```

---

## ✅ Step 4: Verify the Server

If the `--syndication` flag was used, the **ATC** terminology should have been imported.
You can test this using the following command:

```bash
curl --location 'http://localhost:8080/fhir/CodeSystem/$validate-code?=null' \
--header 'Content-Type: application/json' \
--data '{
  "resourceType": "Parameters",
  "parameter": [
    {
      "name": "coding",
      "valueCoding": {
        "system": "http://whocc.no/atc",
        "code": "J07CA09"
      }
    },
    {
      "name": "displayLanguage",
      "valueString": "en, en-US"
    },
    {
      "name": "default-to-latest-version",
      "valueBoolean": true
    },
    {
      "name": "cache-id",
      "valueId": "b86ac430-2d44-4343-a344-544cab8c36db"
    },
    {
      "name": "includeDesignations",
      "valueBoolean": true
    },
    {
      "name": "diagnostics",
      "valueBoolean": true
    }
  ]
}'
```

**Expected response**:

```json
{
  "resourceType": "Parameters",
  "parameter": [
    {
      "name": "result",
      "valueBoolean": true
    },
    {
      "name": "code",
      "valueCode": "J07CA09"
    },
    {
      "name": "system",
      "valueUri": "http://whocc.no/atc"
    },
    {
      "name": "version",
      "valueString": "0"
    },
    {
      "name": "display",
      "valueString": "diphtheria-haemophilus influenzae B-pertussis-poliomyelitis-tetanus-hepatitis B"
    }
  ]
}
```

---

## 📌 Legal Notice

By using Snowstorm, you acknowledge and agree to the **terms and conditions** of each terminology source you import or use.

