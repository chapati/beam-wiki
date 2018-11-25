# Desktop Wallet app

| File | Platform | Location |
|-----|-----|-----|
| Main Executable | Windows | `\Program Files\Beam\Beam Wallet.exe`| 
| Main Executable | Mac | `/Applications/Beam Wallet.app` |
| Main Executable | Linux | `/usr/bin/BeamWallet` |
| Configuration file | ? | ? |
| Logs | Windows | `C:\Users\\{your User name}\AppData\Local\Beam Wallet\logs` | 
| Logs | Mac | `/Users/{your User name}/Library/Application Support/Beam Wallet/logs` |
| Logs | Linux | `/home/{your User name}/.local/share/Beam Wallet/logs` |
| Database | Windows | `\Users\{your User name}\AppData\Local\Beam Wallet\` | 
| Database | Mac | `/Users/{your User name}/Library/Application Support/Beam Wallet/` |
| Database | Linux | `/home/{your User name}/.local/share/Beam Wallet/` |
| Dumps | Windows | `\Users\{your User name}\AppData\Local\Beam Wallet\` |

Points to mention:
* The default location of the Desktop Wallet app can be modified during the installation process (Windows only).
* The default Database location for the Desktop Wallet app can be changed setting the `appdata` parameter to `beam-wallet.cfg` (all platforms).

# Node or CLI wallet

| File | Platform | Location |
|-----|-----|-----|
| Main Executable folder | Windows | `\Program Files\Beam\` |
| Main Executable folder | Mac | ? |
| Main Executable folder | Linux | `/usr/bin/` |
| Logs | Windows | `C:\Users\{your User name}\AppData\Local\Beam Wallet\logs` | 
| Logs | Mac | `/Users/{your User name}/Library/Application Support/Beam Wallet/logs` |
| Logs | Linux | `/home/{your User name}/.local/share/Beam Wallet/logs` |
| Database | Windows | `\Users\{your User name}\AppData\Local\Beam Wallet\` | 
| Database | Mac | `/Users/{your User name}/Library/Application Support/Beam Wallet/` |
| Database | Linux | `/home/{your User name}/.local/share/Beam Wallet/` |
| Dumps | Windows | `\Users\{your User name}\AppData\Local\Beam Wallet\` |

Points to mention:
* Configuration files are located in the same folder with the main executable and are named `beam-node.cfg` or `beam-wallet.cfg`
