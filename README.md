Ansible Role: Librenms for Kubernetes
=====================================

Ansible role to install Librenms on Kubernetes.

Role Variables
--------------

```yaml
# Namespace
kubernetes_librenms_namespace: "default"
# App name (used as selector)
kubernetes_librenms_app: "librenms"
# Deployment name
kubernetes_librenms_deployment: "librenms-deployment"
# Configmap name
kubernetes_librenms_configmap: "librenms"
# Secret name
kubernetes_librenms_secret: "librenms"
# Service name
kubernetes_librenms_service: "librenms"

# Number of replicas
kubernetes_librenms_replicas: 1
kubernetes_librenms_revision_history: 1

# Node selector
kubernetes_librenms_node_selector: {}

# Add custom labels in the deployment metadata section
kubernetes_librenms_deployment_labels: {}
# Add custom annotations in the deployment metadata section
kubernetes_librenms_deployment_annotations: {}

kubernetes_librenms_resources:
  limits:
    memory: "756Mi"
  requests:
    memory: "256Mi"

# Setup ingress for librenms
kubernetes_librenms_setup_ingress: false
kubernetes_librenms_ingress:
  name: "librenms-ingress"
  host: "librenms.example.com"
  annotations:
  tls:

### Volumes ###

# For each volume, `definition` and `subPath` can be used. Their content is
# exactly what would be in a Kkubernetes pod manifest.

# Librenms RRD (see examples for more details)
kubernetes_librenms_rrd_volume:
  definition:

### Librenms options ###

# Librenms database
kubernetes_librenms_db:
  host:
  name:
  user:
  password:

# Number of pollers to run in cron
kubernetes_librenms_pollers: 16

# Enable syslog on port 514
kubernetes_librenms_enable_syslog: 0
```

Dependencies
------------

Kubectl needs to be installed on the host targeted by the role.


Example Playbook
----------------

```yaml

- hosts: kube-master
  run_once: true
  vars:
    kubernetes_librenms_rrd_volume:
      subPath: rrd
      definition:
        glusterfs:
          endpoints: gluster-example-cluster
          path: librenms
          readOnly: false

    kubernetes_librenms_db:
      host: mysql.example.com
      name: librenms
      user: librenms
      password: librenms

    kubernetes_librenms_pollers: 16

    kubernetes_librenms_setup_ingress: true
    kubernetes_librenms_ingress:
      name: "librenms-ingress"
      host: "librenms.example.com"
      annotations:
        kubernetes.io/tls-acme: "true"
      tls:
        - secretName: "librenms-ingress-tls"
          hosts:
            - "librenms.example.com"
  roles:
    - role: Anthony25.kubernetes-librenms
```

Use `run_once` to run the role on only one available master in the cluster.

License
-------

Tool under the BSD license. Do not hesitate to report bugs, ask me some
questions or do some pull request if you want to!
