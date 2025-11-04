# HP Scan Bridge for Paperless-ngx

This is a simple Docker container that acts as a bridge between an HP network scanner (using the eSCL protocol) and a Paperless-ngx instance.

The main purpose is to **make a "dumb" network printer/scanner smarter** by providing a simple API endpoint for direct integration with Paperless. It was successfully tested with an **HP Deskjet 3630** but should work with most modern HP network scanners that support eSCL.

It starts a small web server. When a `POST` request is sent to `/start_scan`, the bridge triggers a scan, downloads the resulting PDF, and uploads it directly to Paperless-ngx using the API.

## Usage

**Prerequisite:** This script relies on the scanner's eSCL protocol. This is the same protocol used by the **HP Webscan** feature. You must **enable "Webscan"** in your scanner's web configuration page (by visiting the scanner's IP address in a browser) for this bridge to function.

1.  Clone this repository.
2.  Create a `.env` file in the same directory as the `docker-compose.yml`.
3.  Fill the `.env` file with your values:

    ```
    SCANNER_IP=YOUR_SCANNER_IP
    PAPERLESS_URL=http://YOUR_PAPERLESS_IP_OR_DOMAIN:PORT
    PAPERLESS_TOKEN=YOUR_PAPERLESS_API_TOKEN
    ```

4.  Start the container:

    ```bash
    docker compose up -d
    ```

## Triggering a Scan

To start a scan, send a POST request to the server (the default port is 5000):

```bash
curl -X POST http://localhost:5000/start_scan