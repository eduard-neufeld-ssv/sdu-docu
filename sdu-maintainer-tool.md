# SDU Management Tool

The SDU Management Tool is used to upload software (SDU archives created with the SDU Signing Tool) to the SDU Server and to manage/distribute them. The tool uses the [SDU REST API](https://github.com/SSV-embedded/SDU-API).


To bundle and sign a single binary or a directory with software we need:
- SDU Management Tool tool for Linux or Windows. Can be downloaded [here](https://hidrive.ionos.com/share/ts9yh380dk).
- SDU maintainer PFX key (certificate) with corresponding password.
- The exact key name of the product.
- SDU server address

- Needed information and data (for Example below): 
	- Firmware archive to upload `dummy_0.0.0.sdu`
	- Key: `mykey.pfx` with password `mypass`
	- Product key `dummy`
	- Firmware Version: `0.0.0`
	- Server address: `mysdu.ssv-service.de`
	- Serialnumber of device we want to update: `879002525AABBCC`

## Manage under Linux
- Get the command line tool `sdu-management-tool`
- Upload firmware to SDU server
```bash
./sdu-management-tool upload -H mysdu.ssv-service.de -f dummy_0.0.0.sdu -k mykey.pfx -P mypass
```
- Set uploaded firmware available
```bash
./sdu-management-tool version-enable -H mysdu.ssv-service.de -p dummy -v 0.0.0
```
- Assign firmware version to device
```bash
./sdu-management-tool assign-append -H mysdu.ssv-service.de  -p dummy -k mykey.pfx -v 0.0.0 -s 879002525AABBCC
```
- Other commands check `sdu-management-tool -h`

## Manage under Windows
- Download and install the application `SDU Management Tool Setup 4.0.1.exe`
- Launch the application `SDU Management Tool` and configure the app under `Settings`
![MUC Settings](img/muc-settings.png)
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
	- Import the product list under `Settings` > `Import products`.
	- Enter the SDU Server address under `Settings` > `Server address`.
	- Select the certificate for communication with the server and enter the associated password.


![MUC Firmware](img/muc-firmware.png)

- To upload firmware to SDU server (Example): 
	- Select the product `My Dummy Device`
	- Upload a signed firmware archive `dummy_0.0.0.sdu`
	- Activate uploaded firmware by checking the available checkbox.

- Assign firmware version to one or multiple devices seriennumber by creating an **Assignment**. The filter is a regular expression and is evaluated from top to bottom.
