
## atahan/knight
An Ansible playbook that sets up an Ubuntu-based home media server/NAS with reasonable security, auto-updates, e-mail notifications for S.M.A.R.T. and Snapraid errors and dynamic DNS. 

It assumes a fresh Ubuntu Server 22.04 install, access to a non-root user with sudo privileges and a public SSH key. This can be configured during the installation process.

## Special thanks
* David Stephens for his [Ansible NAS](https://github.com/davestephens/ansible-nas) project. This is where I got the idea and "borrowed" a lot of concepts and implementations from.
* Wolfgang for his [infra](https://github.com/notthebee/infra) project. This is where I got my insperation. 
* TylerAlterio for the [mergerfs](https://github.com/tyalt1/mediaserver/tree/master/roles/mergerfs) role.
* Jake Howard and Alex Kretzschmar for the [snapraid](https://github.com/RealOrangeOne/ansible-role-snapraid/commits?author=IronicBadger) role.


## Services included:

#### Media
* [Plex](https://hub.docker.com/r/linuxserver/plex) (A media server)
* [Jellyfin](https://hub.docker.com/r/linuxserver/jellyfin) (Yet another media server)
* [Radarr](https://hub.docker.com/r/linuxserver/radarr) (A movie tracker/downloader)
* [Jackett](https://hub.docker.com/r/linuxserver/jackett) (A torrent/NZB indexer)
* [Booksonic](https://hub.docker.com/r/linuxserver/booksonic) (An audiobook server)
* [Sonarr](https://hub.docker.com/r/linuxserver/sonarr) (A TV show tracker/downloader)
* [deluge](https://hub.docker.com/r/linuxserver/deluge) (A torrent downloader)

#### Services
* [Heimdall](https://hub.docker.com/r/linuxserver/heimdall) (Home Server Dashboard)
* [PiHole + Unbound](https://github.com/chriscrowe/docker-pihole-unbound) (An all-in-one DNS solution with built-in ad-blocking)
* [Wireguard](https://hub.docker.com/r/linuxserver/wireguard) (A VPN server)

### Misc
* [Watchtower](https://hub.docker.com/r/containrrr/watchtower) (An automated updater for Docker images)



## Other features:
* MergerFS with Snapraid
* Samba
*

## Usage
Install Ansible (macOS):
```
brew install ansible
```

Install the dependencies:
```
ansible-galaxy install -r requirements.yml
```
Finally, run the playbook:
```
ansible-playbook run.yml -l your-host-here -K
```
The "-K" parameter is only necessary for the first run, since the playbook configures passwordless sudo for the main login user

