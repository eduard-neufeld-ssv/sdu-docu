# SDU Signing Tool

The SDU Signing Tool is a application for packaging and signing firmware into an SDU archive with the extension `*.sdu`. These archives can be uploaded to the SDU Server using the [SDU Maintainer Tool](sdu-maintaner-tool.md).

To bundle and sign a single binary or a directory with software we need:
- SDU Signing tool for Linux or Windows. Can be downloaded [here](https://hidrive.ionos.com/share/ts9yh380dk).
- SDU maintainer PFX key (certificate) with corresponding password.
- The exact key name of the product.

- Needed information and data (for Example below): 
	- Bundle a file `myfirmware_0.0.0.sqsh`
	- Key: `mykey.pfx` with password `mypass`
	- Product key `dummy`
	- firmware Version: `0.0.0`

## Bundle an sign under Linux
- Get the command line tool `sdu-signing-tool`
- Create a SDU archive `dummy_0.0.0.sdu`
```bash
./sdu-signing-tool -f myfirmware_0.0.0.sqsh -p dummy -v 0.0.0 -k mykey.pfx -P mypass
```

## Bundle an sign under Windows
- Download and install the application `SDU Signing Tool Setup 4.0.1.exe`
- Launch the application `SDU Signing Tool`
![SDU Signing Tool](img/sdu-signing-tool.png)
- Get or create a product list as JSON file. Create a JSON file e.g. `product.json` with content:
	```json
	{
	"products": [
		{
			"name": "dummy",
			"text": "My Dummy Device"
		}
	]
	}
	```
	- `name`: Product name following JavaScript naming conventions (see https://de.wikipedia.org/wiki/Namenskonvention_(Datenverarbeitung))
	- `text`: Display name shown in the tools. Free to choose.
- Import the product list under **Product** and select the product.
- Select the PFX certificate to be used for signing and enter the corresponding password.
- Choose the directory where the created SDU archives should be saved.
- Specify the version and, optionally, a comment.
- Add files to the SDU archive with **File**, or add the contents of a directory with **Dir**.
- Create a signed SDU archive with **Create & Sign**.

