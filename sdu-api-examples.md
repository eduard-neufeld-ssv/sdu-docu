# SDU API Examples

Examples with Bash, OpenSSL and Curl.
* Information:
	- [SDU Manifest File](sdu-manifest.md) 
	- [SDU REST API](https://github.com/SSV-embedded/SDU-API)
* Prerequisites:
	- Firmware BLOB file to be uploaded: `myfirmware.tar`
	- Maintainer key for signing and uploading: `mykey.pfx`
	- SDU Server CA certificate for uploading: [ssv-server-pki.crt.pem](ssv-server-pki.crt.pem)
	- SDU Server address: `https://ssvdev-sdu0.ssv-service.de`

Key and certificate are extracted from PFX first since OpenSSL cannot sign with PFX directly.
```bash
openssl pkcs12 -in mykey.pfx -nodes -nocerts -out mykey.key
openssl pkcs12 -in mykey.pfx -clcerts -nokeys -out mykey.crt
```
Set environment variables to make the following commands easier to use:
```bash
PATHCA=ssv-server-pki.crt.pem
PATHCRT=mykey.crt
PATHKEY=mykey.key
SDUSERVER=https://ssvdev-sdu0.ssv-service.de
```

## Display Products and Versions
- Display products (the jq tool is only for formatting the output):
```bash
curl -s --cacert $PATHCA --cert $PATHCRT --key $PATHKEY $SDUSERVER/v2/product | jq
```
- Display versions of a product named `dummy`:
```bash
PRODUCT=dummy
curl -s --cacert $PATHCA --cert $PATHCRT --key $PATHKEY $SDUSERVER/v2/product/$PRODUCT/version | jq
```

## Upload Blob and Manifest
* Create manifest file
```bash
BLOB=myfirmware.tar
PRODUCT=dummy
VERSION=1.2.3
SHA256=$(sha256sum $BLOB | cut -d' ' -f1)
cat > $BLOB.manifest <<-EOF
{
    "product": "$PRODUCT",
    "version": "$VERSION",
    "sha256": "$SHA256",
    "comment": ""
}
EOF
```

* Sign manifest file.
```bash
openssl cms -sign -signer $PATHCRT -inkey $PATHKEY <$BLOB.manifest >$BLOB.manifest.cms
```

* Upload blob
```bash
curl -s -X POST --cacert $PATHCA --cert $PATHCRT --key $PATHKEY --data-binary @$BLOB $SDUSERVER/v2/blob/$SHA256
```

* Upload manifest
```bash
curl -s -X POST --cacert $PATHCA --cert $PATHCRT --key $PATHKEY --data-binary @$BLOB.manifest.cms -H "Content-Type: application/cms" $SDUSERVER/v2/product/$PRODUCT/version/$VERSION/manifest
```
