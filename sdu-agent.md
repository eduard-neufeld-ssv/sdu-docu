# SDU Agent

The SDU Agent is a program created specifically for each update process on a component/machine/system. The agent is executed/controlled in the gateway by the [GUC](sdu-guc.md) (Gateway Update Client) and provides a special interface (SDU Agent API) for this purpose. Multiple agents can be installed in the system.

## SDU Agent API
An SDU Agent has the following tasks:

* Provide information about the currently installed version
* Install a new version

Communication takes place through call parameters, standard input/output/error and the program's exit code.

### Query Current Version

The query allows the SDU Gateway Update Client to find out which product the agent serves, which version is installed, and if applicable, where an image of the currently installed version can be found. The latter is needed to compress updates using deltas.

* **Call**: `/path/to/agent info`
* **STDOUT**:
```js
{
	"product": "rmg941",
	"version": "3.2.5", // Optional (if not specified -> update in any case)
	"path": "/dev/mmcblk0p3", // Optional
	"sha256": "ba7816bf8f01cfea414140de5dae2223b00361a396177a9cb410ff61f20015ad" // Optional
}
```
* **STDERR**: Error messages in case the exit code is not 0.
* **Exit**:
   - `0`: OK
   - `1`: Busy
   - `2`: WrongConfiguration

### Write New Version

The query allows the SDU Gateway Update Client to install a new update. This will happen when the version returned by the `info` command differs from the version specified by the SDU Server.

* **Call**: `/path/to/agent install [version] [sha256]`
   - `[version]`: Version string of the update to be installed. The agent can already decide whether to accept the update based on this. For example, downgrades can be prevented if the firmware cannot handle them.
   - `[sha256]`: The SHA256 hash of the update. The agent must verify that the received data actually produces this hash. To prevent a full download for hash verification (which may not be possible for large updates), the Gateway Update Client does not perform the verification task.
* **STDIN**: Data stream of the new image
* **STDERR**: Status messages about the current step
* **STDOUT**: Actions that can be communicated to the GUC
   - `REBOOT`: Restart the gateway after successful update
* **Exit**:
   - 0: Done
   - 1: Failed
   - 2: RejectWrongVersion
   - 3: RejectWrongSHA256
