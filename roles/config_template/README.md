# config_template

This role manages config-key definitions used by Cephadm in services which it manages.

A full list of available templates can be found [here](https://docs.ceph.com/en/latest/cephadm/services/monitoring/#option-names)

## Prerequisites

### Host Requirements

- SSH access with passwordless `sudo`.
- `cephadm` CLI installed and accessible.
- Connectivity between bootstrap node and all Ceph nodes.

### Inventory Requirements

- The bootstrap node must be defined using the `cephadm_bootstrap_host` variable.
- All applicable hosts should be part of the `ceph` group.

## Role variables

**List** of config templates to apply.

Each item can include:

| Key             | Required | Description                                                                 |
|----------------|----------|-----------------------------------------------------------------------------|
| `config_key`    | ✔️       | The full config-key path to write to. Example: `services/ingress/keepalived.conf`. |
| `contents`      | ✴️       | Raw YAML/text content to be copied as file.                                 |
| `local_path`    | ✴️       | Path to a Jinja2 template to render and copy.                               |
| `reconfig`      | ✴️       | Boolean. If true, restarts services post apply. Default: false.             |
| `services`      | ✴️       | List of Ceph services to restart if `reconfig: true`.                        |

✴️ Either `contents` or `local_path` must be defined per entry.

---

> [!TIP]
> Encase existing templates in `{% raw %}` `{% endraw %}` tags to preserve
> cephadm tempating. Open and close the tags to include Ansible driven
> templating.

## Example
```yaml
cephadm_config_template:
  - config_key: prometheus/prometheus.yml
    local_path: cephadm/prometheus.yml.j2
    services:
      - rgw.ingress
      - prometheus
    reconfig: true
  - config_key: mgr/mgr.conf
    contents: |
      [mgr]
      mgr_modules = dashboard
