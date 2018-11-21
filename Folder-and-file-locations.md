| File | Windows | Mac | Linux |
|-----|-----|-----|-----|
| Main Executable |\Program Files\Beam\Beam Wallet.exe| /Applications/Beam Wallet.app | /usr/bin/BeamWallet |
| Configuration file |\Program Files\Beam\beam-wallet.cfg | - | /usr/bin/beam-wallet.cfg|
| Logs |C:\Users\\{your User name}\AppData\Local\Beam Wallet\logs|/Users/{your User name}/Library/Application Support/Beam Wallet/logs |/home/{your User name}/.local/share/Beam Wallet/logs|
| Dumps and Databases  |\Users\{your User name}\AppData\Local\Beam Wallet\ |/Users/{your User name}/Library/Application Support/Beam Wallet/|/home/{your User name}/.local/share/Beam Wallet/|

Logs for cli applications are placed in the same folder as application.
Dumps are created only for Windows. For Ui wallet and beam-node.exe. For Ui it's placed in \Users\{your User name}\AppData\Local\Beam Wallet\, for cli - in the same folder as beam-node.exe.

Main executable for windows can be changed during installation. It's default path in the table.
We have not only beam-wallet.cfg, but also beam-node.cfg. Cfg file should be located in the same folder as application.

Database place can be changed if user add parameter appdata in beam-wallet.cfg for UI wallet