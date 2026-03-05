# Manifest

The manifest contains all meta-information for a provided update.
It is created by the Signing Tool (SK) and evaluated by the Gateway Update Client (GUC),
which then passes the corresponding information to the SDU Agent.

The manifest is an S/MIME-signed JSON file. Signing must be done by a **Maintainer**.

## Manifest Contents
Body of S/MIME file:

```js
{
	"product": "dummy", // Product name
	"version": "1.2.4", // Version name
	"sha256": "e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855", // SHA256 of the data
	"comment": "" // Optional: comment for the version
	// Product-specific fields can be added if needed
}
```
