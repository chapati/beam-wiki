# All files

| File | Windows | Mac | Linux |
|-----|-----|-----|-----|
| Main Executable | `\Program Files\Beam\Beam Wallet.exe`| `/Applications/Beam Wallet.app` | `/usr/bin/BeamWallet` |
| Configuration file | `\Program Files\Beam\beam-<node_or_wallet>.cfg` (located at the same folder as the executable) | N/A | `/usr/bin/beam-<node_or_wallet>.cfg` |
| Database | `\Users\{your User name}\AppData\Local\Beam Wallet\` | `/Users/{your User name}/Library/Application Support/Beam Wallet/` | `/home/{your User name}/.local/share/Beam Wallet/` |
| Logs | see below | see below | see below |
| Dumps | see below | N/A | N/A |

Points to mention:
* Default location of the Desktop Wallet app can be modified during the installation process (Windows only).
* The default Database location for the Desktop Wallet app can be changed setting the `appdata` parameter to `beam-wallet.cfg` (all platforms).

# Logs

| Executable | Platform | Log location |
|-----|-----|-----|
| Node or CLI wallet | all platforms | the logs are in the same folder as the applications |
| Desktop Wallet app | Windows | `C:\Users\\{your User name}\AppData\Local\Beam Wallet\logs` | 
| Desktop Wallet app | Mac | `/Users/{your User name}/Library/Application Support/Beam Wallet/logs` |
| Desktop Wallet app | Linux | `/home/{your User name}/.local/share/Beam Wallet/logs` |

# Dumps:

Dumps are created on Windows only:

| Executable | Dump location |
|-----|-----|
| Wallet Desktop app | `\Users\{your User name}\AppData\Local\Beam Wallet\` |
| Node, CLI wallet | Same folder with the executable |



