in  ## Run playbook to setup monitoring

```sh
ansible-playbook -i inventory books/setup-monitoring.yml --limit master --tags influxdb,grafana,kapacitor --extra-vars "influxdbhost=10.238.114.188 kapacitorhost=10.238.114.188"
ansible-playbook -i inventory books/setup-monitoring.yml --limit env --tags telegraf --extra-vars "influxdbhost=10.238.114.188 kapacitorhost=10.238.114.188"
```

## Run playbook to setup zap-localproxy

```sh
ansible-playbook -i inventory books/setup-zap.yml
```

## Run playbook to setup Pastel

```sh
ansible-playbook -i inventory books/setup-pastel.yml
```

## Run playbook to setup poViewer

```sh
ansible-playbook -i inventory books/setup-poviewer.yml --extra-vars "version=latest"
```

## Run playbook to purge servers

```sh
ansible-playbook -i inventory books/run-purge.yml
```
