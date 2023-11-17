# st-custom-ubuntu

This project creates a custom Ubuntu ISO that can be used to quickly install Ubuntu Server 22.04.3. The custom ISO automatically selects all installation options based on pre-determined values, installs proprietary services from a private APT repository, and installs a temporary private key for the initial reverse ssh tunnel creation.

After the installation and auto-run of the post-install script are finished, a reverse ssh tunnel will automatically be created to the pre-determined central management server (created by the installed proprietary services, via systemd), then the central management server will detect the tunnel creation and ssh to the machine through the provided tunnel to: 1) rename the machine (i.e. hostname of the machine) from `temporary-hostname` to a uniquely identifiable name that is managed by the central management server; 2) change the main sudo user's password from `temporary-password` to a strong password that is managed by the central management server; 3) replace the temporary private key with multiple unique private keys that are managed by the central management server; then 4) the remaining proprietary services will be configured to begin running at pre-determined times (via systemd).

## How to use

- download ubuntu 22.04.3: `wget -O ubuntu.iso https://releases.ubuntu.com/22.04.3/ubuntu-22.04.3-live-server-amd64.iso`
  - note: debian-installer/preseed.cfg no longer used starting 22.04; use subiquity/autoinstall.yaml instead
- mount the iso: `mkdir mnt && sudo mount -o loop ubuntu.iso mnt`
- extract iso contents: `mkdir ubuntu-iso-contents && cp -rT mnt ubuntu-iso-contents/`
- unmount the iso: `sudo umount mnt` ("umount" is not a typo; the command is indeed "umount" and not "unmount")
- add custom files: `cp autoinstall.yaml ubuntu-iso-contents/ && cp /path-to-key/temporary-private-key ubuntu-iso-contents/`

## Notes

- This solution was ideal for the purpose of provisioning bare metal machines on the scale of hundreds, wherein the machines would then be physically deployed and installed to various geographic locations that are connected only via the Internet (i.e. "connected" to each other and our central management server).
  - Different requirements may have called for different tools and methods (e.g. PXE boot server, etc.).
- The "proprietary services" include tools for remote troubleshooting and maintenance of machines, and interactive software services for use by users co-located with the deployed machines.