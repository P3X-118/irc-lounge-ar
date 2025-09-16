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

This is an [Ansible](https://www.ansible.com/) role which installs [The Lounge](https://thelounge.com/) to run as a [Docker](https://www.docker.com/) container wrapped in a systemd service.

The Lounge is a free and open-source collaborative wiki and documentation software, designed for seamless real-time collaboration. It can be used to manage a wiki, a knowledge base, project documentation, etc. It has various functions such as granular permissions management system, page history to track changes of articles, etc. It also supports diagramming tools like Draw.io, Excalidraw and Mermaid.

See the project's [documentation](https://thelounge.com/docs/) to learn what The Lounge does and why it might be useful to you.

## Prerequisites

To run a The Lounge instance it is necessary to prepare a [Postgres](https://www.postgresql.org/) database server and [Redis](https://redis.io/) server for managing a metadata database.

If you are looking for Ansible roles for them, you can check out [ansible-role-postgres](https://github.com/mother-of-all-self-hosting/ansible-role-postgres) and [ansible-role-redis](https://github.com/mother-of-all-self-hosting/ansible-role-redis), both of which are maintained by the [Mother-of-All-Self-Hosting (MASH)](https://github.com/mother-of-all-self-hosting) team. The roles for [KeyDB](https://keydb.dev/) ([ansible-role-keydb](https://github.com/mother-of-all-self-hosting/ansible-role-keydb)) and [Valkey](https://valkey.io/) ([ansible-role-valkey](https://github.com/mother-of-all-self-hosting/ansible-role-valkey)) are available as well.

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

### Set variables for connecting to a Redis server

As described above, it is necessary to set up a [Redis](https://redis.io/) server for managing a metadata database of a The Lounge instance. You can use either KeyDB or Valkey alternatively.

Having configured it, you need to add and adjust the following configuration to your `vars.yml` file, so that the The Lounge instance will connect to the server:

```yaml
thelounge_redis_username: ''
thelounge_redis_password: ''
thelounge_redis_hostname: YOUR_REDIS_SERVER_HOSTNAME_HERE
thelounge_redis_port: 6379
thelounge_redis_dbnumber: ''
```

Make sure to replace `YOUR_REDIS_SERVER_HOSTNAME_HERE` with the hostname of your Redis server. If the Redis server runs on the same host as The Lounge, set `localhost`.

### Configure a storage backend

The service provides these storage backend options: local filesystem (default) and Amazon S3 compatible object storage.

#### Amazon S3 compatible object storage

To use Amazon S3 or a S3 compatible object storage, add the following configuration to your `vars.yml` file (adapt to your needs):

```yaml
thelounge_environment_variable_storage_driver: s3

# Set a S3 access key ID
thelounge_environment_variable_aws_s3_access_key_id: ''

# Set a S3 secret access key ID
thelounge_environment_variable_aws_s3_secret_access_key: ''

# Set the the region where your S3 bucket is located
thelounge_environment_variable_aws_s3_region: ''

# Set a S3 bucket name to use
thelounge_environment_variable_aws_s3_bucket: ''

# The endpoint URL for your S3 service (optional; set if using a S3 compatible storage like Wasabi and Storj)
thelounge_environment_variable_aws_s3_endpoint: ''

# Control whether to force path style URLs (https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/Config.html#s3ForcePathStyle-property) for S3 objects
thelounge_environment_variable_aws_s3_force_path_style: false
```

### Configure the mailer

You can configure a mailer for functions such as user invitation. The Lounge supports a SMTP server (default) and Postmark. To set it up, add the following common configuration and settings specific to SMTP server or Postmark to your `vars.yml` file as below (adapt to your needs):

```yaml
thelounge_mailer_enabled: true

# Set the email address that emails will be sent from
thelounge_environment_variable_mail_from_address: hello@example.com

# Set the name that emails will be sent from
thelounge_environment_variable_mail_from_name: thelounge
```

#### Use SMTP server (default)

To use a SMTP server, add the following configuration to your `vars.yml` file:

```yaml
# Set the hostname of the SMTP server
thelounge_environment_variable_smtp_host: 127.0.0.1

# Set the port to use for the SMTP server
thelounge_environment_variable_smtp_port: 587

# Set the username for the SMTP server
thelounge_environment_variable_smtp_username: ''

# Set the password for the SMTP server
thelounge_environment_variable_smtp_password: ''

# Control whether TLS is used when connecting to the server
thelounge_environment_variable_smtp_secure: false

# Control whether SSL errors are ignored
thelounge_environment_variable_smtp_ignoretls: false
```

⚠️ **Note**: without setting an authentication method such as DKIM, SPF, and DMARC for your hostname, emails are most likely to be quarantined as spam at recipient's mail servers. If you have set up a mail server with the [MASH project's exim-relay Ansible role](https://github.com/mother-of-all-self-hosting/ansible-role-exim-relay), you can enable DKIM signing with it. Refer [its documentation](https://github.com/mother-of-all-self-hosting/ansible-role-exim-relay/blob/main/docs/configuring-exim-relay.md#enable-dkim-support-optional) for details.

#### Use Postmark

To use Postmark, add the following configuration to your `vars.yml` file:

```yaml
thelounge_environment_variable_mail_driver: postmark

# Set the token for Postmark
thelounge_environment_variable_postmark_token: ''
```

### Extending the configuration

There are some additional things you may wish to configure about the component.

Take a look at:

- [`defaults/main.yml`](../defaults/main.yml) for some variables that you can customize via your `vars.yml` file. You can override settings (even those that don't have dedicated playbook variables) using the `thelounge_environment_variables_additional_variables` variable

See its [environment variables](https://thelounge.com/docs/self-hosting/environment-variables) for a complete list of The Lounge's config options that you could put in `thelounge_environment_variables_additional_variables`.

## Installing

After configuring the playbook, run the installation command of your playbook as below:

```sh
ansible-playbook -i inventory/hosts setup.yml --tags=setup-all,start
```

If you use the MASH playbook, the shortcut commands with the [`just` program](https://github.com/mother-of-all-self-hosting/mash-playbook/blob/main/docs/just.md) are also available: `just install-all` or `just setup-all`

## Usage

After running the command for installation, The Lounge becomes available at the specified hostname like `https://example.com`.

To get started, open the URL on a web browser and create a first workspace by inputting required information. For an email address, make sure to input your own email address, not the one specified to `thelounge_environment_variable_mail_from_address`.

## Troubleshooting

### Check the service's logs

You can find the logs in [systemd-journald](https://www.freedesktop.org/software/systemd/man/systemd-journald.service.html) by logging in to the server with SSH and running `journalctl -fu thelounge` (or how you/your playbook named the service, e.g. `mash-thelounge`).
