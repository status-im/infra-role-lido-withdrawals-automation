# Description

This role configures the [`lido-withdrawals-automation`](https://github.com/status-im/lido-withdrawals-automation) command-line tool as a cron job.

The tool assists in the generation of pre-signed exit messages that will be used by the LIDO ejector tool whenever the LIDO smart contracts indicate that a particular validator must be withdrawn from the protocol (to serve a request for a sizable liquid staking withdrawal). In particular, the tools performs the following steps:

- Fetches validator data from the KAPI endpoint
- Creates withdrawal messages and signs them using the remote signer or the Keymanager API running on one or more hosts
- Encrypts the signed messages using the specified password
- Saves the encrypted messages to a specified output folder. The ejector must be configured to look for exit messages in the same folder.

## Details

The tool runs as a docker container from [`Dockerfile`](templates/Dockerfile.j2).
Ansible runs the container once and then creates a crontab to run the container periodically.
After the `lido-withdrawals-automation` container finish, the `Ejector` container is restarted (to pick up the newly created exit messages).
The script started by `cron` is generated from the [`templates/containers.sh.j2`](templates/containers.sh.j2) template.

## Configuration

In order for `lido-withdrawals-automation` to function, the following variables must be specified by users of this role:

- `percentage`
- `kapi_url`
- `remote_signer_url`
- `key_manager_urls`
- `password`
- `output_folder`
- `operator_id`
- `beacon_node_url`
- `module_id`

> You must define at least one of the "remote_signer_url" or "key_manager_urls" variables.
> If the specified configuration is incomplete, the container will exit immediately with the exit code 130.
