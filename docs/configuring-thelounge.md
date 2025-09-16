<!--
SPDX-FileCopyrightText: 2020 - 2024 MDAD project contributors
SPDX-FileCopyrightText: 2020 - 2024 Slavi Pantaleev
SPDX-FileCopyrightText: 2020 Aaron Raimist
SPDX-FileCopyrightText: 2020 Chris van Dijk
SPDX-FileCopyrightText: 2020 Dominik Zajac
SPDX-FileCopyrightText: 2020 Mickaël Cornière
SPDX-FileCopyrightText: 2022 François Darveau
SPDX-FileCopyrightText: 2022 Julian Foad
SPDX-FileCopyrightText: 2022 Warren Bailey
SPDX-FileCopyrightText: 2023 Antonis Christofides
SPDX-FileCopyrightText: 2023 Felix Stupp
SPDX-FileCopyrightText: 2023 Pierre 'McFly' Marty
SPDX-FileCopyrightText: 2024 - 2025 Suguru Hirahara

SPDX-License-Identifier: AGPL-3.0-or-later
-->

# Setting up The Lounge

This is an [Ansible](https://www.ansible.com/) role which installs [The Lounge](https://thelounge.chat/) to run as a [Docker](https://www.docker.com/) container wrapped in a systemd service.

The Lounge is a modern web IRC client designed for self-hosting. It implements features such as push notification, link previews, and file uploading, and keeps a persistent connection to the IRC server while you are offline (meaning you do not need a bouncer). It is a progressiv web app (PWA), and can be accessed via a browser.

See the project's [documentation](https://thelounge.chat/docs) to learn what The Lounge does and why it might be useful to you.

## Prerequisites

## Adjusting the playbook configuration

To enable The Lounge with this role, add the following configuration to your `vars.yml` file.

**Note**: the path should be something like `inventory/host_vars/mash.example.com/vars.yml` if you use the [MASH Ansible playbook](https://github.com/mother-of-all-self-hosting/mash-playbook).

```yaml
########################################################################
#                                                                      #
# thelounge                                                            #
#                                                                      #
########################################################################

thelounge_enabled: true

########################################################################
#                                                                      #
# /thelounge                                                           #
#                                                                      #
########################################################################
```

### Set the hostname

To enable the The Lounge instance you need to set the hostname as well. To do so, add the following configuration to your `vars.yml` file. Make sure to replace `example.com` with your own value.

```yaml
thelounge_hostname: "example.com"
```

After adjusting the hostname, make sure to adjust your DNS records to point the domain to your server.

**Note**: hosting The Lounge under a subpath (by configuring the `thelounge_path_prefix` variable) does not seem to be possible due to The Lounge's technical limitations.

### Extending the configuration

There are some additional things you may wish to configure about the component.

Take a look at:

- [`defaults/main.yml`](../defaults/main.yml) for some variables that you can customize via your `vars.yml` file. You can override settings (even those that don't have dedicated playbook variables) using the `thelounge_environment_variables_additional_variables` variable

## Installing

After configuring the playbook, run the installation command of your playbook as below:

```sh
ansible-playbook -i inventory/hosts setup.yml --tags=setup-all,start
```

If you use the MASH playbook, the shortcut commands with the [`just` program](https://github.com/mother-of-all-self-hosting/mash-playbook/blob/main/docs/just.md) are also available: `just install-all` or `just setup-all`

## Usage

After running the command for installation, The Lounge becomes available at the specified hostname like `https://example.com`.

To get started, run the command below to create a first user:

```sh
ansible-playbook -i inventory/hosts setup.yml --tags=add-thelounge -e username=USERNAME_HERE password=PASSWORD_HERE
```

After the user is configured, it will be possible to log in to the instance.

## Troubleshooting

### Check the service's logs

You can find the logs in [systemd-journald](https://www.freedesktop.org/software/systemd/man/systemd-journald.service.html) by logging in to the server with SSH and running `journalctl -fu thelounge` (or how you/your playbook named the service, e.g. `mash-thelounge`).
