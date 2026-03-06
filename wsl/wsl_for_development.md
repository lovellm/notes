# Setup WSL for Development

Some things work better or only work in Linux.
So use Linux while in Windows.

## Install WSL

- Install [Windows Terminal](https://learn.microsoft.com/en-us/windows/terminal/install) if not already installed.
- Start with [WSL Install Documentation](https://learn.microsoft.com/en-us/windows/wsl/install).
- Log in to the new install (if not already in it) by entering command `wsl` in Windows Terminal.
- Add custom certificate chain if needed.
  - Copy the certificate file from Windows to WSL
    `sudo cp /mnt/c/Users/YourUser/YourCertFile.pem /usr/local/share/ca-certificates/certificate.crt`.
  - Make sure to update the path and certificate file name as appropriate.
  - Run `sudo update-ca-certificates` to update the certificate store with that cert.


> [!TIP]
> To copy/paste in Windows Terminal, use Right-Click
  (copies highlighted text, pastes if no text highlighted).
  Ctrl+C, Ctrl+V will NOT work.

## Change Shell to Use Zsh

- Update packages using `sudo apt update`.
- Install Zsh: `sudo apt install -y zsh`.
- Switch to it: `chsh -s $(which zsh)`.
- Install Oh My Zsh:
  `sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"`.
  - Say `Y` when asked to make it the default.
- (Optional) Change Theme: `nano ~/.zshrc`
  - Find `ZSH_THEME="robbyrussell"` (that is the default, it could be using something else).
  - Change the `robbyrussell` to the theme that you want. Recommended: `eastwood` or `cloud`.
  - See [Official Documentation](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes) for list of themes.
  - Save and close the file: Crtl+S, Ctrl+X.
- Restart the terminal (or `source ~/.zshrc` to reload config).

## Install Node using fnm

- See [Official Documentation](https://nodejs.org/en/download) for more details.
- Install fnm: `curl -o- https://fnm.vercel.app/install | zsh`.
  - If you get an error about missing unzip, `sudo apt install unzip`, then try again.
- Restart the terminal or run the command it provides in order for your shell to be aware of it.
- Install node: `fnm install 25`.
- Restart the terminal (or `source ~/.zshrc` to reload config).
  Otherwise npm from Windows may get used and break everything.
- Verify node: `node -v`. Should be v25.x.y.
  - If you get an error about missing shared library libatomic.so.1:
    `sudo apt install libatomic1`, then try again.
- Verify npm: `npm -v`. Should be 11.x.y.
- Add custom SSL certificates (if needed).
  - Copy the certificate file from Windows to WSL
    - `mkdir ~/certs`
    - `cp /mnt/c/Users/YourUser/YourCertFile.pem ~/certs/certificate.pem`.
    - Make sure to update the path and certificate file name as appropriate.
  - `export NODE_EXTRA_CA_CERTS="~/certs/certificate.pem"`

## Setup Git and SSH Key

- In Windows, make sure you have git set up and configured to use Windows SSH and [Windows SSH Agent](https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_keymanagement).
  - In Windows Powershell, `ssh-add -l`. You should see your key. If not, the below will not work.
  - In WSL, `ssh-add.exe -l`. It should also show your key. If not, the below will not work.
- In WSL, tell git to use SSH from Windows: `git config --global core.sshCommand "ssh.exe"`.
- Test SSH connection: `ssh.exe -T git@github.com`.
  - If using Github Enterprise Server, replace github.com with the correct address.
  - You should get a message that authentication was successful. If not, start over with the Windows SSH Agent link above.
- Make a folder to keep your repositories in. Recommended: `mkdr ~/git`. Other good folders may be `~/code` or `~/dev`. Take you pick, whichever makes you happy.
- Go to that directory and clone a repository: `cd ~/git`, `git clone [repo ssh linke here]`.

> [!TIP]
> If git config --list commands open in VIM and you do not know what you are doing,
  `j` to scroll down, `k` to scroll up, `:q` to exit VIM.

### Backup Option

If the above worked, great, ignore this.

- Copy keys from Windows to WSL.
  - From the WSL Terminal: `cp -r /mnt/c/Users/YourUser/.ssh ~/.ssh`.
  - Change permissions in WSL: `chmod 600 ~/.ssh/id_rsa`.
    Change the file name as needed and repeat for multiple keys if needed.
- Either enter passphrase every time you use it, or search for online for instructions on setting up a ssh agent.

# Using WSL from Windows

## Use WSL from VSCode

- Search for the extension "WSL" by Microsoft and install it.
- From a WSL terminal, in the folder you want to use, type `code .` and that folder will open in VSCode.
- VSCode extensions will need to be installed in WSL.
  VSCode shows a "Install in WSL" button by the extension, clicking it will do this for you.

## Access WSL Servers from Windows

When testing what you develop, it will run in WSL.
If you need browser access to it you will need to forward the ports.

- Using a Client/Server app with VITE as an example.
  Server runs on port 3000, VITE uses default port 5173.
- [VITE Server Host](https://vite.dev/config/server-options.html#server-host)

- Add `--host` to an appropriate vite script in `package.json`
  Example: `"wsl": "vite --host",`.

If the workspace is open in VSCode port forwarding happens automatically.
VSCode will notifiy you when a port opens and list all the forwarded ports.

- This requires having VSCode logged in to Github.
- [VSCode Port Forwarding](https://code.visualstudio.com/docs/debugtest/port-forwarding)

### Manually Forwarding Ports

This has not been tested because it just works when using VSCode.

- [WSL Port Forwarding](https://learn.microsoft.com/en-us/windows/wsl/networking#accessing-a-wsl-2-distribution-from-your-local-area-network-lan)
- Add portproxy to netsh
  - For Server: `netsh interface portproxy add v4tov4 listenport=3000 listenaddress=0.0.0.0 connectport=3000 connectaddress=(wsl hostname -I)`
  - For UI: `netsh interface portproxy add v4tov4 listenport=5173 listenaddress=0.0.0.0 connectport=5173 connectaddress=(wsl hostname -I)`

- Possibly useful `netsh` commands. Not tested.
  - [netsh interface](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/netsh-interface)
  - `netsh interface portproxy show all`
    - displays existing portproxy rules
  - `netsh interface portproxy reset`
    - resets all portproxy rules to their defaults (clears them?)
  - `netsh interface delete v4tov4 listenport=3000 listenaddress=0.0.0.0`
    - delete a specific portproxy rule, opposite of add.

## Key Pair Access to a Database

- Copy the private key from Windows to WSL
  - `cp /mnt/c/Users/YourUser/.ssh/key_name.p8 ~/.ssh/key_name.p8`
- When you reference the key, you must use the full path without the `~` shortcut
  - `/home/YourUser/.ssh/key_name.p8`

Beyond that it works just the same as Windows.
