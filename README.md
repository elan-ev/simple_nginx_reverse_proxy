# Ansible role for a simple nginx reverse proxy installation

![molecule](https://github.com/elan-ev/simple_nginx_reverse_proxy/actions/workflows/molecule.yml/badge.svg)

This role installs nginx as a simple reverse proxy.

## Role Variables

For a full overview of configuration options look at the [defaults](defaults/main.yml).

### Proxy Configuration

This role conmes with a default configuration automatically redirects to HTTPS
and which allows you to specify a simple block to configure Nginx:

```yaml
nginx_proxy: |
  location / {
    proxy_set_header        Host $host;
    proxy_set_header        X-Real-IP $remote_addr;
    proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header        X-Forwarded-Proto $scheme;
    proxy_pass              http://127.0.0.1:8080;
  }
```

### TLS Certificates

The default configuration provides simple, self-signed certificates if none exist.
Please make sure to replace them with your own certificates.
Simply overwrite the following files:

- `/etc/nginx/tls/certificate.key;`
- `/etc/nginx/tls/certificate.crt;`

### Advanced Configuration

You can set the `nginx_config` option to overwrite the default configuration
and specify your own set of configuration files.
Set a list of `src` and `dest` fields specifying templates to deploy.
The field `dest` always specifies a location relative to `/etc/nginx/`:

```
roles:
  - role: elan.simple_nginx_reverse_proxy
    nginx_config:
      - src: nginx.conf
        dest: nginx.conf
```

Be aware that the role includes an [`nginx.conf` template](templates/nginx.conf).
Specifying `src: nginx.conf` will **always** use the included template.


### Security Related Configuration

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
      configure_for_firewalld: true
```


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

## License

[BSD-3-Clause](LICENSE)

## Author Information

[ELAN e.V](https://elan-ev.de/)
