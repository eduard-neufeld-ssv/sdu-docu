# SDU Server

The Secure Device Update Server manages and provides firmware for devices to be updated. The SDU Server is operated via a [REST API](https://github.com/SSV-embedded/SDU-API) and is available as a Docker image on Docker Hub (https://hub.docker.com/r/ssvembeddedde/ssv-sdu-server/).

The workflow for operating the server is as follows:
- Start the SDU Server Docker container on a host. The host must have a valid FQDN.
- Provide the host's FQDN to SSV. SSV issues a license and installs it on the server. Keys for accessing the server are also created.
- After that, the SDU Server is ready for operation.

## Starting the server
### 1. Configure the SDU Server
Create a data directory; in this example `data/`, where the container will store its data. The stored data includes the server configuration and software uploaded by users:

```bash
mkdir -p data
```

Create a Docker Compose configuration `docker-compose.yml` with the following content:

```yml
version: '2.1'
services:
  sdu-server:
    image: ssvembeddedde/ssv-sdu-server:1.0.2
    container_name: sdu-server
    volumes:
      - ./data:/opt/sdu-server/data
    ports:
      - 9080:9080/tcp
      - 443:443/tcp
    restart: always
```

- `image`: `ssvembeddedde/ssv-sdu-server:<version>` — choose the latest available version
- `volumes`: `<local directory>:/opt/sdu-server/data`
- `ports`: the following ports must be exported:
  - `9080`: configuration port
  - `443`: SDU API port

### 2. Start the SDU Server

```bash
docker-compose up -d
```

After starting, the data directory will contain an `events` database and a `license` key.

```bash
ls data/
events  license
```

To test whether the server is reachable from the Internet, open the URL `http://<FQDN>:9080/` in a browser. The request is answered with:

```json
{"version":1,"error":"Endpoint does not exist"}
```

### 3. Get and install a license
- Request server license. Retrieve request and send to SSV `support@ssv-embedded.de` for licensing.
```bash
FQDN=<FQDN>
curl -f -X GET "http://$FQDN:9080/license/getRequest?fqdn=$FQDN" >$FQDN.request
```
- Once you got the license `<FQDN>.license` from SSV apply it to the server:
```bash
FQDN=<FQDN>
curl -f -X POST -H 'Content-Type:application/pkcs7-mime' "http://$FQDN:9080/license/setLicense" --data-binary @$FQDN.license
```

### 4. Initial upload
For blob diff downloads to work, an initial NULL blob must be uploaded against diffs are created.

Example using `curl`; the maintainer key and certificate files are required:
```bash
CERT=<maintainer.crt.pem>
KEY=<maintainer.key.pem>
SERVER=<FQDN>

touch zero
curl -s -X POST --cacert ssv-server-pki.crt.pem --cert $CERT --key $KEY --data-binary @zero "https://$SERVER/v2/blob/$(sha256sum zero | cut -d' ' -f1)"
```
