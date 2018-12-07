Since the current versions of the Desktop Wallet are not backward compatible, it is required to remove the files manually before upgrading to the next major version.

Points to mention:
* On Mac and Windows the wallet files reside in directories which are not shown by default, yet they can be accessed via a command line interface (see the examples below).
* On Mac and Windows the wallet files reside (by default) in a home directory of the user. In the examples below the user name is `sergei-beam` and `serge` on `Mac` and `Windows` respectively.
* The suggested commands will unconditionally remove the files, an action which cannot be undone.

# On Windows
Run the following command:
``` sh
RD /s /q "C:\Users\serge\AppData\Local\Beam Wallet"
```

# On Mac
Run the following command:
``` sh
rm -R "/Users/sergei-beam/Library/Application Support/Beam Wallet"
```

# On Linux
Run the following command:
``` sh
rm -R ".local/share/Beam Wallet"
```
