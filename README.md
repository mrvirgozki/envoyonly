# 🚀 VIRGOZKI ENVOY-ONLY PROXY + gRPC (Cloud Run)

Isang high-performance, containerized proxy setup na nagpapatakbo ng **Envoy Proxy** at **Xray Core** sa ibabaw ng **Debian Bookworm-slim**, na naka-optimize para sa **Google Cloud Run**. Ang setup na ito ay gumagamit ng Envoy bilang nag-iisang Edge Router para sa lahat ng traffic (WebSockets, HTTPUpgrade, XHTTP, at gRPC) nang walang Nginx/OpenResty.

---

## 📂 Project Structure

Ang project na ito ay binubuo ng mga sumusunod na malinis at naayos na files:

*   **`Dockerfile`** — Multi-stage Docker build na nag-i-install ng Xray, Envoy, at mga kinakailangang Linux packages sa Debian slim.
*   **`envoy.yaml`** — Envoy configuration na nagha-handle ng routing para sa mga inbounds ng Xray (Trojan, Vmess, Vless, Shadowsocks, at gRPC) pati na rin ang mga koneksyon tulad ng XHTTP (`stream-one` / `stream-up`).
*   **`config.json`** — Xray core configuration na may kasamang multi-protocol support, ad-blocking DNS rules, at custom paths.
*   **`entrypoint.sh`** — Script na nagko-configure ng dynamic port/binding para sa Cloud Run at nagpapatakbo ng configuration tests bago simulan ang serbisyo.
*   **`supervisord.conf`** — Supervisor configuration para sabay na patakbuhin ang Xray at Envoy sa loob ng iisang container.
*   **`deploy.sh`** — Automation script para sa pag-check ng files, pagbuo ng image sa Google Artifact Registry, pagpili ng CPU/RAM specs, at pag-deploy sa Cloud Run.
*   **`anti_ddos.py`** — Monitoring script para subaybayan ang active TCP connections ng system.
*   **`index.html`** — Default landing page o fallback file kapag binisita ang root URL ng service.

---

## 🛠️ Requirements

1.  **Google Cloud SDK (`gcloud`)** na naka-install at naka-authenticate.
2.  Aktibong **GCP Project** na may naka-enable na Billing.

---

## ⚙️ Paano Gamitin at I-deploy

1.  **I-clone o ilagay lahat ng files** sa iisang folder sa iyong computer. *(Siguraduhing natanggal mo na ang lumang `nginx.conf` dahil hindi na ito ginagamit).*
2.  Gawing executable ang deploy script (kung kinakailangan):
    ```bash
    chmod +x deploy.sh
    ```
3.  Patakbuhin ang deploy script:
    ```bash
    DEPLOY=true ./deploy.sh
    ```
    *   *Note: Piliin ang iyong nais na Cloud Run resource specification (CPU / RAM profile) kapag tinanong sa terminal (Default: Standard 2 CPU, 4Gi RAM).*

---

## 🌐 Public & Internal Routing

*   **Public URL (`Cloud Run`):** `https://<your-service-url>` (Pinapadaan sa Envoy Port `8080`)
*   **Health Check:** `https://<your-service-url>/health` (Nagbabalik ng `ok`)
*   **Xray Inbound Ports (Internal):** `10000` hanggang `10015`
