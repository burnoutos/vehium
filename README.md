<p align="center">
  <img src="https://raw.githubusercontent.com/burnoutos/vehium/main/docs/img/vehium_logo_bgrem.png" alt="Vehium" width="80" />
</p>

<h1 align="center">Vehium</h1>

<p align="center">
  <strong>The Operating System for Modern Workshops</strong>
  <br />
  Garage management platform with real-time vehicle status notifications.
  <br />
  Web &middot; Windows &middot; Android &middot; iOS
</p>

<p align="center">
  <a href="https://github.com/burnoutos/vehium/releases/latest"><img src="https://img.shields.io/github/v/release/burnoutos/vehium?style=flat-square&color=14d5ff&label=latest" alt="Latest Release" /></a>
  <a href="https://github.com/burnoutos/vehium/releases"><img src="https://img.shields.io/github/downloads/burnoutos/vehium/total?style=flat-square&color=00f4b8&label=downloads" alt="Downloads" /></a>
  <a href="https://github.com/burnoutos/vehium/blob/main/LICENSE"><img src="https://img.shields.io/github/license/burnoutos/vehium?style=flat-square&color=7c3aed" alt="License" /></a>
</p>

<p align="center">
  <a href="https://vehium.com">Website</a> &nbsp;&middot;&nbsp;
  <a href="https://app.vehium.com">Web App</a> &nbsp;&middot;&nbsp;
  <a href="#download">Download</a> &nbsp;&middot;&nbsp;
  <a href="mailto:info@vehium.com">Contact</a>
</p>

---

## What is Vehium?

Vehium is a smart garage management platform that helps workshops manage their entire operation from one place. From client and vehicle management to service orders, invoicing, analytics, and real-time customer notifications — across every platform.

**For workshops:**
- Manage clients, vehicles, and service history
- Create and track service orders with photo inspections
- Generate PDF invoices with VAT and Stripe integration
- Assign roles and permissions (Admin, Manager, Worker)
- Access analytics and revenue dashboards
- Use on Web, Windows desktop, or mobile

**For customers:**
- Track vehicle service status in real time
- Receive push notifications on updates
- View and download invoices
- Browse complete vehicle history
- Discover workshops nearby
- 100% free companion app

---

## Download

<a id="download"></a>

### Workshop App

| Platform | Download | Requirements |
|----------|----------|--------------|
| **Windows** | [**Vehium Setup (x64)**](https://github.com/burnoutos/vehium/releases/latest/download/Vehium-Setup-x64.exe) | Windows 10/11 (64-bit) |
| **Web** | [Web Browser](https://app.vehium.com) | Any modern browser |
| **iOS** | [AppStore](https://apps.apple.com/us/app/vehium-workshop/id6761576687) | iOS 15.0+ |
| **Android** | [PlayStore](https://play.google.com/store/apps/details?id=com.atheod.BurnoutOSPortal) | Android 7.0+ |

### Customer App

| Platform | Download | Requirements |
|----------|----------|--------------|
| **Web** | [Web Browser](https://customer.vehium.com) | Any modern browser |
| **iOS** | [iOS AppStore](https://apps.apple.com/us/app/vehium/id6761552709) | iOS 15.0+ |
| **Android** | [PlayStore](https://play.google.com/store/apps/details?id=com.atheod.BurnoutOSCustomer) | Android 7.0+ |

> Want early access? Email us at **[info@vehium.com](mailto:info@vehium.com?subject=Vehium%20Internal%20Testing%20Access)** to join our internal testing program — we'll get back to you within 24 hours.

---

## Platforms

| | Windows | Web | iOS | Android |
|---|:---:|:---:|:---:|:---:|
| Workshop App | **Yes** | **Yes** | **Yes** | **Yes** |
| Customer App | — | **Yes** | **Yes** |  **Yes** |
| Real-time Sync | **Yes** | **Yes** | **Yes** | **Yes** |

---

## Installation

### Windows (EXE)

1. **Download** the installer from the link above.
2. Windows SmartScreen may show a warning — click **More info > Run anyway**.
3. Follow the installation wizard.
4. Open **Vehium Workshop** from the desktop or Start menu.

> Detailed guide: [Windows Workshop](README-windows-workshop.md)

### iOS & Android

Coming soon to the App Store and Google Play worldwide. To join our internal testing program, email **[info@vehium.com](mailto:info@vehium.com?subject=Vehium%20Internal%20Testing%20Access)** — we'll get back to you within 24 hours.

> Detailed guide: [Mobile Apps (iOS & Android)](README-mobile.md)

---

## Infrastructure

Vehium runs on **two self-hosted Kubernetes clusters on bare metal** — a primary production cluster and a fully independent disaster-recovery cluster — both designed for high availability and rapid failover.

### Architecture

```
            vehium.com / app.vehium.com / customer.vehium.com
                                │
                       Load Balancer + TLS
                                │
        ┌───────────────────────┼───────────────────────┐
        │                       │                       │
   ┌─────────┐             ┌─────────┐             ┌─────────┐
   │  Node 1 │             │  Node 2 │             │  Node 3 │
   │  MASTER │◄───────────►│  MASTER │◄───────────►│  MASTER │
   │  DB PRI │   etcd q.   │  DB REP │   etcd q.   │  DB REP │
   └─────────┘             └─────────┘             └─────────┘
        │                       │                       │
        └────── Replicated block storage (3× copies) ───┘
                                │
              ┌─────────────────┴─────────────────┐
              │                                   │
       Continuous WAL                    Daily snapshots
       streaming + PITR                  (DB / volumes / etcd)
              │                                   │
              └────────► Encrypted offsite backups (EU region)
                                │
                                ▼
               ┌──────────────────────────────────┐
               │   Independent DR Cluster (3-node)│
               │   Cold standby, restore-ready    │
               └──────────────────────────────────┘
```

### Tech Stack

| Layer | Technology |
|-------|-----------|
| Orchestration | Kubernetes — 3 control-plane nodes, fully HA |
| Virtual IP | Floating control-plane VIP (kube-vip) |
| Service Load Balancing | MetalLB in L2 mode |
| Ingress | Reverse proxy with automatic TLS certificate renewal |
| Database | PostgreSQL with streaming replication, managed by an operator |
| Database Backups | Continuous WAL archiving + point-in-time recovery |
| Block Storage | Longhorn — distributed, replicated 3× across nodes |
| Cluster Backups | Volume snapshots + full-cluster backup tooling (Velero) |
| Offsite Backups | Encrypted daily backups to S3-compatible object storage (Backblaze B2, EU region) |
| Deployment | GitOps — `git push` reconciles the cluster (Argo CD, HA mode) |
| Observability | Prometheus + Grafana with multi-channel alerting |
| Power | UPS-protected nodes with NUT-coordinated graceful shutdown |
| Disaster Recovery | Independent secondary 3-node cluster, ready to restore from offsite backups |

### Backup Strategy

Vehium follows a **defence-in-depth** backup model — multiple independent layers, each protecting against a different failure mode:

1. **In-cluster replication** — every database write streams synchronously to two replicas; every persistent volume keeps three live copies on different nodes.
2. **Continuous database archiving** — write-ahead logs ship continuously to offsite object storage, enabling point-in-time recovery to any moment in the retention window.
3. **Daily logical dumps** — full `pg_dump` snapshots of every database, retained on a rolling schedule.
4. **Daily cluster snapshots** — Velero captures Kubernetes resources and persistent volumes, encrypted at rest.
5. **etcd snapshots** — the cluster's source of truth is snapshotted on a fixed cadence and pushed offsite.
6. **Geographic separation** — all offsite backups live in a different country from the primary cluster, in encrypted buckets with versioning and lifecycle policies.

### Disaster Recovery

A fully independent **second 3-node Kubernetes cluster** stands by as a cold-standby. It runs the same operating system, container runtime, and tooling as production, and is wired into the same GitOps repository — so a recovery boils down to:

1. Restore offsite backups (database, volumes, etcd) into the standby cluster.
2. Argo CD reconciles application manifests from git automatically.
3. DNS is repointed once health checks pass.

Recovery objectives:

- **RPO** (max data loss) — minutes, thanks to continuous WAL streaming.
- **RTO** (time to restore service) — a few hours for a full from-scratch rebuild, far less for partial failures.

### Resilience

- **Node failure** — etcd quorum holds, pods reschedule automatically, the database promotes a replica within seconds, and the floating VIP moves to a healthy node.
- **Disk failure** — replicated storage continues serving from surviving replicas and rebuilds the missing replica in the background.
- **Power outage** — UPS triggers a coordinated graceful shutdown across the cluster; nodes auto-restart and rejoin once power returns.
- **Network blip** — kube-vip + MetalLB re-elect endpoints; clients see at most a brief reconnect.
- **Full primary cluster loss** — restore from offsite backups into the DR cluster; GitOps brings applications back automatically.
- **Backup destination loss** — backup targets are versioned and lifecycle-managed; multiple independent backup types mean no single corruption can take everything out.

### Observability

- Metrics, logs, and dashboards via Prometheus and Grafana.
- Alert rules cover node health, storage capacity, certificate expiry, replication lag, backup freshness, and application SLOs.
- Alerts fan out over email and other channels so issues surface within minutes — even outside working hours.

### GitOps Deployment

Every change to production — application code, infrastructure configuration, secrets references — flows through git. Argo CD continuously reconciles the cluster against the repository, which means:

- Every change is reviewed, versioned, and reversible.
- Rollback is a `git revert`.
- The cluster is reproducible from source.

---

## Languages

Vehium is fully localized in **8 languages**:

English &middot; Greek &middot; German &middot; Italian &middot; French &middot; Spanish &middot; Russian &middot; Bulgarian

---

## Support

Having trouble? Reach out directly at **[info@vehium.com](mailto:info@vehium.com)**

---

## License

This project is licensed under the [MIT License](LICENSE).

<p align="center">
  <sub>Made with care by the Vehium team.</sub>
</p>
