# Ansible role for a simple nginx reverse proxy installation

![molecule](https://github.com/elan-ev/simple_nginx_reverse_proxy/actions/workflows/molecule.yml/badge.svg)

This role installs nginx as a simple reverse proxy.
It is basically a clone of https://github.com/elan-ev/opencast_nginx without the Opencast-specific stuff.

## Role Variables

For a full overview of configuration options look at the [defaults](defaults/main.yml).

### Custom Configuration

You can set the `nginx_config` option to overwrite the default configuration.
Set a list of `src` and `dest` fields specifying templates to deploy.
The field `dest` always specifies a location relative to `/etc/nginx/`:

```
roles:
  - role: elan.simple_nginx_reverse_proxy
    nginx_config:
      - src: nginx.conf
        dest: nginx.conf
```

### Security and Firewall Related

This role can open ports for Nginx in firewalld or ufw.
It can also set the SELinux boolean to allow Nginx to act as a reverse proxy.
These settings are disabled by default and you have to explicitely enable them:

- `configure_for_firewalld: true`
- `configure_for_ufw: true`
- `configure_for_selinux: true`

## Example Playbook

Your playbook might look like this:

```yaml
---

- hosts: all
  become: true
  roles:
    - role: elan.simple_nginx_reverse_proxy

```

In this case you would have a configuration template for promtail that is located in a folder `my_templates` relative to the playbook.

## Development

For development and testing you can use [molecule](https://molecule.readthedocs.io/en/latest/).
With podman as driver you can install it like this – preferably in a virtual environment:

```bash
pip install -r .dev_requirements.txt
```

Then you can *create* the test instances, apply the ansible config (*converge*) and *destroy* the test instances with these commands:

```bash
molecule create
molecule converge
molecule destroy
```

If you want to inspect a running test instance use `molecule login --host <instance_name>`, where you replace `<instance_name>` with the desired value.

## License

[BSD-3-Clause](LICENSE)

## Author Information

[ELAN e.V](https://elan-ev.de/)
