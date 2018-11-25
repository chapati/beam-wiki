| File | Windows | Mac | Linux |
|-----|-----|-----|-----|
| Main Executable | `\Program Files\Beam\Beam Wallet.exe` (default location, can be changed during the installation process) | `/Applications/Beam Wallet.app` | `/usr/bin/BeamWallet` |
| Configuration file (located at the same folder with the executable) | `\Program Files\Beam\beam-[node|wallet].cfg`  | N/A | `/usr/bin/beam-[node|wallet].cfg` |
| Logs (for cli applications are placed in the same folder as application) | `C:\Users\\{your User name}\AppData\Local\Beam Wallet\logs` | `/Users/{your User name}/Library/Application Support/Beam Wallet/logs` | `/home/{your User name}/.local/share/Beam Wallet/logs` |
| Database the default location can be changed adding the `appdata` parameter to `beam-wallet.cfg` for Desktop Wallet app | `\Users\{your User name}\AppData\Local\Beam Wallet\` | `/Users/{your User name}/Library/Application Support/Beam Wallet/` | `/home/{your User name}/.local/share/Beam Wallet/` |
| Dumps | see below | N/A | N/A |

Dump locations for Windows executables:
| Executable | Dump |
|-----|-----|
| Wallet Desktop app | \Users\{your User name}\AppData\Local\Beam Wallet\ |
| Node, CLI wallet | Same folder with the executable |



