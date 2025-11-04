# HP Scan Bridge for Paperless-ngx

This is a simple Docker container that acts as a bridge between an HP network scanner (using the eSCL protocol) and a Paperless-ngx instance.

The main purpose is to make a "dumb" network printer/scanner smarter by providing a simple API endpoint for direct integration with Paperless. It was successfully tested with an **HP Deskjet 3630** but should work with most modern HP network scanners that support eSCL.

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
    HOST_PORT=PORT_ON_HOST_FOR_POST_REQUEST
    ```

4.  Start the container:

    ```bash
    docker compose up -d
    ```

## Triggering a Scan

To start a scan, send a POST request to the server (the default port is 5000):

```
curl -X POST http://localhost:5000/start_scan
```

## Use Case: Home Assistant Integration

This endpoint is ideal for integration into smart home systems like Home Assistant. You can create a simple `rest_command` to trigger the scan.

### 1. Add to your `configuration.yaml`:

Add the following to your `configuration.yaml` file, replacing `YOUR_DOCKER_HOST_IP` with the IP address of the machine running this Docker container.

```
# configuration.yaml
rest_command:
  start_hp_scan:
    url: 'http://YOUR_DOCKER_HOST_IP:5000/start_scan'
    method: post

```
### 2. Restart Home Assistant:


Restart your Home Assistant instance to load the new service.

### 3. Create a Dashboard Button (Optional):

You can add a button to your Lovelace dashboard to trigger the scan from your UI.
### Example Dashboard Button

```
type: button
name: Start Scan to Paperless
icon: mdi:scanner
tap_action:
  action: call-service
  service: rest_command.start_hp_scan
```