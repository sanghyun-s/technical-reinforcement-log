> **Coursework · CIS 9760 Big Data Technologies · Module 5 — OpenSearch + Dockerized Python client** · Tag: 🛡️ **Portfolio-Defense**
> Companion: [Module2-3_AWS-terminal](../aws/Module2-3_AWS-terminal.md) · [Module4_docker-api-app](../docker/Module4_docker-api-app.md) · index: [coursework-README](../../_legacy/coursework-README-v1.md)
> *Converted from the original `.docx` to clean markdown; all technical content, the five debugging
> issues, and the conclusions preserved.*

# CIS 9760 Module 5 — Provisioning & Navigating an OpenSearch Cluster

**Environment:** AWS `us-east-2` (Ohio) · EC2 · Session Manager · File Browser · Docker.

**Why 🛡️:** this is search-infrastructure + containerized client + secret management on a managed
cloud service — exactly the deployment/ops layer my §12.2 names. It also connects directly to
CASSIA's retrieval story (a managed search/vector service queried over REST).

## Objective — three layers, connected

```
Amazon OpenSearch
     ↓  Python requests / REST API
Dockerized Python client
     ↓  running on
AWS EC2
```
Provision an OpenSearch domain, explore sample flight data via Dashboards, query indexed documents
through multiple interfaces, then connect from a Python app inside a Docker container on EC2.

## 1 — Provisioning the cluster
OpenSearch domain, Ohio region, dev config: `Dev/Test` · 1-AZ · `t3.small.search` · 1 node · public
access · fine-grained access control enabled · master user created manually.
- The **domain endpoint** is the URL apps/REST use; the **Dashboards URL** is the browser
  management/visualization interface — two different URLs for two different jobs.
- **Provisioning is not instantaneous** — the domain needs time to initialize before endpoint and
  dashboard are available. (Operational patience is part of ops.)

## 2 — Exploring Dashboards
Loaded `opensearch_dashboards_sample_data_flights`; explored via Dashboard / Discover / Dev Tools /
Visualizations. A representative document: `FlightNum`, `OriginCityName`, `DestCityName`,
`AvgTicketPrice`, `DistanceMiles`, `FlightDelay`, `Cancelled`, `Carrier`, `Origin`, `Dest`. Recorded
a document `_id` (required later for REST retrieval; the assignment required using my *own* dataset's
`_id`, not the instructor's).

## 3 — Querying with Dev Tools
Query types and when each applies:
- **`match`** — text-oriented matching.
- **`term`** — exact field value.
- **`range`** — numeric values within boundaries.
- **document lookup by `_id`** — returns the same underlying document seen in Discover, but as raw
  JSON. Reinforced that **Dashboards is a GUI over REST API operations.**

## 4 — The REST endpoint structure
```
DOMAIN_ENDPOINT + / + INDEX_NAME + /_doc/ + DOC_ID
# e.g. https://<domain-endpoint>/opensearch_dashboards_sample_data_flights/_doc/<DOC_ID>
```
Append index + document path to the domain endpoint to retrieve a specific document. This becomes
critical during the Docker debugging phase (Issue 1).

## 5 — The Dockerized Python client
On EC2: `/home/ubuntu/files/opensearch/` with a `Dockerfile` + `main.py`.
```
FROM python:3.9 → install requests → WORKDIR /app → COPY main.py → run Python app
```
`main.py` used `requests.get(...)` with `requests.auth.HTTPBasicAuth(...)` to retrieve a document.

## Debugging reinforcement — five issues, five layers

**Issue 1 — Duplicated OpenSearch path.** `ES_HOST` accidentally held the *full* document path
(`domain/index/_doc/id`) while the code *also* appended `/{INDEX_NAME}/_doc/{DOC_ID}`, producing
`domain/index/_doc/id/index/_doc/id` → `no handler found for uri`.
**Lesson:** `ES_HOST` must contain **only the domain endpoint**; Python constructs the full URL.

**Issue 2 — Placeholder text in the hostname.** An abbreviated endpoint containing `...` landed in
`ES_HOST` → `LocationParseError`, because `search-...us-east-2.es.amazonaws.com` isn't a valid
hostname. **Lesson:** never shorten a hostname inside executable code — docs abbreviate URLs,
config needs the exact endpoint.

**Issue 3 — One-character domain typo.** After fixing the placeholder → `NameResolutionError`
(`...otyzq` vs the real `...otyza`). Copying the exact endpoint from the working browser URL fixed
DNS resolution.
**Lesson — the key distinction:**
`LocationParseError` → **malformed hostname syntax** · `NameResolutionError` → **syntactically
valid hostname, but DNS can't find it.** Read Python networking errors from the *bottom* of the
traceback; don't treat every connection failure as an OpenSearch problem.

**Issue 4 — Stale Docker image.** Each `main.py` change needed `docker build -t opens:1.0 .` before
`docker run opens:1.0`, or Docker kept executing the previous snapshot. **Lesson:** `main.py` on EC2
≠ `main.py` already baked into the image. *(Same snapshot/rebuild principle as the Quarto render-trap
in STA MP#00 and the Module 4 image lesson — three places, one idea.)*

**Issue 5 — Shell smart-quotes.** Copied text contained curly `"` instead of straight `"`, and one
password value wasn't closed. Bash didn't recognize the smart quote as a delimiter, so later terminal
text became part of the unfinished command — Docker eventually read `build` as an image name and
tried to pull `build:latest`. **Lesson:** shell syntax is extremely typography-sensitive; prefer
straight single quotes for env values with special characters (`-e ES_PASSWORD='value'`).

## 6 — Moving secrets to environment variables (Deliverables 10 → 11)
Refactored from hard-coded credentials to:
```
os.environ['DOMAIN_ENDPOINT']   os.environ['ES_USERNAME']   os.environ['ES_PASSWORD']
DOC_ID = sys.argv[1]            # document id as a runtime argument
```
Architecture shift: **credentials-in-source → container environment (`os.environ`) + command
argument (`sys.argv`)**. Final run pattern:
```bash
docker run \
  -e DOMAIN_ENDPOINT='https://<domain-endpoint>' \
  -e ES_USERNAME='<username>' \
  -e ES_PASSWORD='<password>' \
  opens:1.0 <DOC_ID>
```
Deliverable 10 = successful hard-coded connection (returned `_index`, `_id`, `_version`, `found`,
`_source` + all flight fields); Deliverable 11 = the same via environment + runtime arg.

## AWS cleanup (cost discipline)
After submitting: **EC2 → Stop**, **OpenSearch domain → Delete** — the `t3.small.search` instance is
billable, so cleanup prevents continued charges. Both confirmed initiated.

## Key reinforcement — debug by layer
The assignment connected provisioning → indices/documents → REST endpoint → HTTP GET → Python
`requests` → Basic Auth → Docker image → env variables → runtime args. The biggest lesson:
**identify which layer is actually failing.**

| Symptom | Layer |
|---|---|
| OpenSearch error (`no handler found`) | API / path |
| `LocationParseError` | malformed URL |
| `NameResolutionError` | hostname / DNS |
| Old behavior after editing | stale Docker image |
| Unexpected Docker command interpretation | shell quoting |

## Final takeaway
Started as OpenSearch provisioning; became a reusable cloud-app workflow: **provision → inspect via
GUI → query via REST → connect via Python → package with Docker → separate credentials from source →
debug networking/shell by layer → clean up resources.** The end result — a Dockerized Python client
retrieving a specific document from OpenSearch using runtime config, not hard-coded credentials — is
directly reusable for any containerized service that must talk securely to an external managed service.

## Interview line candidate (🛡️ — not yet promoted)
> I stood up an OpenSearch cluster and queried it from a Dockerized Python client on EC2, with
> credentials in environment variables and the document id as a runtime argument — and the real skill
> was debugging by layer: telling a `LocationParseError` (malformed URL) from a `NameResolutionError`
> (DNS can't find a valid host) from a stale-image bug, instead of treating every failure as "Docker broke."
