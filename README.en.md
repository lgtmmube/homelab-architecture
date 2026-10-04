# Homelab

[日本語](README.md) | **English**

[![Physical layout of the homelab](physical-topology.png)](physical-topology.png)

[![Cluster and observability architecture](kube-obs.png)](kube-obs.png)

The first diagram shows the physical layout of the equipment and network. The second focuses on how telemetry is collected, stored, and visualized.

## About this lab

This is my home lab, built around Kubernetes for application deployment, networking, distributed storage, and observability. It runs on a mix of rack servers, mini PCs, a GPU node, and a Raspberry Pi.

I experiment by deliberately putting the system under load and observing how the applications and infrastructure behave.

## Main components

| Area | Role |
| --- | --- |
| Cluster and networking | Kubernetes (kubeadm) forms the core, with Cilium controlling communication within the cluster. CoreDNS provides name resolution, and kube-vip provides access to the Kubernetes API |
| Configuration management | Ansible prepares the hosts, while Argo CD synchronizes configurations defined with Helm and Kustomize |
| Storage | Rook manages Ceph, providing block storage and S3-compatible object storage |
| Certificates and secrets | cert-manager manages certificates, while Sealed Secrets makes it possible to store encrypted secrets in Git |
| Network and execution visibility | Hubble network logs, Tetragon events from selected applications, and Kubernetes audit logs help track system behavior |

## Observability

The observability stack collects metrics, logs, traces, and profiles. I use Grafana to query and visualize them from their respective backends.

| Signal | Main path | What I look at |
| --- | --- | --- |
| Metrics | Prometheus → Mimir | CPU, memory, disk, and GPU status, along with cluster and storage health |
| Logs and events | Alloy → Loki | Error details, state changes, and records of network traffic and process execution |
| Traces | OpenTelemetry → Alloy → Tempo | The path a request takes and where processing time is spent |
| Profiles | Go pprof → Alloy → Pyroscope | How applications use CPU time and memory |

For day-to-day operation, I check the overall state in Grafana. When something changes unexpectedly, I investigate the related logs, traces, and profiles. Alertmanager sends incident and recovery notifications to Slack.

## Independent monitoring and backups

A Raspberry Pi outside the cluster also runs Blackbox exporter, Prometheus, and Alertmanager. It checks cluster connectivity and the Kubernetes API from the outside, while a DHT22 measures the server room's temperature and humidity. It sends alerts through a separate path from the monitoring inside the cluster. (Unless the switch or router goes down… (*^^*))

For recovery, I encrypt daily backups of etcd snapshots, Sealed Secrets decryption keys, and Grafana's database and configuration, then save them to Releases in a private GitHub repository.

---

## Credits

The product names, logos, and product images in the diagrams belong to their respective rights holders. This repository introduces my personal home lab and does not imply affiliation with or endorsement by any of the projects or companies shown.

<details>
<summary>Logo sources, attribution, and licenses</summary>

| Name or asset | Credits and source |
| --- | --- |
| systemd logomark | Tobias Bernard (2019). [Official artwork](https://brand.systemd.io/) · [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/) |
| GnuPG logo | Thomas Wittek, © 2006 g10 Code GmbH. [GnuPG](https://gnupg.org/) · [Artwork license](https://github.com/gpg/gnupg/blob/master/artwork/README) · [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/) |
| Ansible Community Mark | Ansible Logos Project / Red Hat, Inc. [Official artwork](https://github.com/ansible/logos) · [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/) |
| Argo, Cilium, containerd, Envoy, etcd, Helm, OpenTelemetry, Prometheus, Rook, CoreDNS, cert-manager | Trademarks or registered trademarks of The Linux Foundation. [Official CNCF artwork](https://github.com/cncf/artwork) · [Trademark information](https://www.linuxfoundation.org/legal/trademarks) |
| Kubernetes, Strimzi, kube-vip, Ceph | Trademarks or registered trademarks of LF Projects, LLC. [Trademark information](https://lfprojects.org/policies/trademark-policy/) · [Official Ceph artwork](https://ceph.io/en/logos/) |
| Hubble, Tetragon | Cilium project. [Official brand assets](https://cilium.io/brand/) |
| Prometheus Operator, kube-state-metrics, Kustomize | Their respective projects and the relevant Prometheus and Kubernetes rights holders. [Prometheus Operator](https://github.com/prometheus-operator/prometheus-operator/tree/main/Documentation/logos) · [kube-state-metrics](https://github.com/kubernetes/kube-state-metrics) · [Kustomize](https://kustomize.io/) |
| Grafana, Alloy, Loki, Mimir, Tempo, Pyroscope | Trademarks and logos of Grafana Labs. [Official website](https://grafana.com/) · [Trademark information](https://grafana.com/trademark-policy/) |
| GitHub | Trademark of GitHub, Inc. [Official brand assets](https://brand.github.com/foundations/logo) |
| Slack | Slack is a trademark of Salesforce, Inc. [Trademark information](https://www.salesforce.com/company/legal/intellectual-property/) |
| Raspberry Pi | Raspberry Pi is a trademark of Raspberry Pi Ltd. [Trademark information](https://www.raspberrypi.com/trademark-rules/) |
| Apache Kafka | Apache, Apache Kafka, Kafka, and the Kafka logo are trademarks or registered trademarks of The Apache Software Foundation. [Apache Kafka](https://kafka.apache.org/) · [Trademark information](https://www.apache.org/foundation/marks/) |
| NGINX | Trademark or registered trademark of F5, Inc. [Official website](https://docs.nginx.com/) · [Trademark information](https://www.f5.com/company/policies/trademarks) |
| NVIDIA | Trademark or registered trademark of NVIDIA Corporation. [Official brand assets](https://www.nvidia.com/en-us/about-nvidia/legal-info/logo-brand-usage/) |
| Go | Trademark of Google. [Official logo](https://go.dev/blog/go-brand) · [Brand information](https://go.dev/brand) |
| Temperature sensor symbol | From the [draw.io shape library](https://www.drawio.com/docs/diagram-types/aws-diagrams/) based on [AWS Architecture Icons](https://aws.amazon.com/architecture/icons/) |

The systemd, GnuPG, and Ansible assets have been placed in the diagrams, resized, and exported to PNG. Each asset's license notice applies to that asset.

</details>
