# Ansible role for a simple nginx reverse proxy installation

![molecule](https://github.com/elan-ev/simple_nginx_reverse_proxy/actions/workflows/molecule.yml/badge.svg)

This role installs nginx as a simple reverse proxy.
It is basically a clone of https://github.com/elan-ev/opencast_nginx without the Opencast-specific stuff.

## Role Variables

For a full overview of configuration options look at the [defaults](defaults/main.yml).

### Custom Config Files

Instead of mapping a lot of variables from an ansible-config-file to a nginx-config-file,
you can simply specify the paths to your own config-templates.
Specifically, there are three templates that are copied by this role:

* `nginx_base_config` becomes `/etc/nginx/nginx.conf`
* `nginx_http_config` becomes `/etc/nginx/conf.d/http.conf`
* `nginx_tls_config` becomes `/etc/nginx/conf.d/tls.conf`

The last two are included within the [default base config](templates/nginx.conf), withing the http-block (`http.conf`) and within the server-block for https (`tls.conf`).
If you change the base config, you might need to include these again.
If the variables remain empty (e.g. `nginx_tls_config: ''`), the files are not created on the remote.

Additionally, the default base config includes all files `/etc/nginx/conf.d/locations/*.conf` within the https server block.
This means that you can copy additional location configurations in seperate files there and they will be included in the base configuration.
The role initially copies a `root.conf` file in the locations-folder, but it will not overwrite anything that already exists there.
Also, if you copy a custom `root.conf` in place, it will not be overwritten by the role.

### Security and Firewall Related

This role can enable nginx for firewalld or ufw.
However, you have to tell it so explicitely by either setting `configure_for_firewalld` or `configure_for_ufw` to `true`.
If SELinux is on the system and active, this role automatically enables nginx to act as a reverse proxy.

## Example Playbook

Your playbook might look like this:

```yaml
---

- hosts: all
  become: true
  roles:
    - role: elan.simple_nginx_reverse_proxy
      nginx_tls_config: 'my_templates/nginx_tls_config.yml.j2'

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
