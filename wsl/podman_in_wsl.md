# Using Window's Podman from WSL

[This explains it all](https://podman-desktop.io/docs/podman/accessing-podman-from-another-wsl-instance).

Notes:

- First make sure podman is installed and working in Windows.
- Then follow the instructions in that file.
- The `podman system connection add` command will need to be modified before running.
  - Their default is for a "rootful" machine.
  - The machine name and socket will be different for a "rootless" machine.
  - `ll /mnt/wsl/podman-sockets` to get machine name.
  - `ll /mnt/wsl/podman-sockets/{machine-name}` to get the sockets for that machine.
- The instructions is using group id 10 for access.
  - This should be fine, even though the name of that group may be different.
  - The instructions have a collapsed section on this.
  - Just run the given `usermod` command and hope for the best.

Useful podman commands that I forget because I don't use them often enough:

- [List images](https://docs.podman.io/en/latest/markdown/podman-images.1.html): `podman images -a`
- [Prune Images](https://docs.podman.io/en/latest/markdown/podman-image.1.html): `podman image prune`
- [Remove Images](https://docs.podman.io/en/latest/markdown/podman-rmi.1.html): `podman rmi imageid`
