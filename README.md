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
| **Android** | Coming soon to Google Play | Android 7.0+ |

> Want early access? Email us at **[info@vehium.com](mailto:info@vehium.com?subject=Vehium%20Internal%20Testing%20Access)** to join our internal testing program — we'll get back to you within 24 hours.

---

## Platforms

| | Windows | Web | iOS | Android |
|---|:---:|:---:|:---:|:---:|
| Workshop App | **Yes** | **Yes** | **Yes** | **Yes** |
| Customer App | — | **Yes** | **Yes** | Coming Soon |
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

Vehium runs on a self-hosted **3-node Kubernetes high-availability cluster** on bare metal.

### Architecture

```
         vehium.com / app.vehium.com / customer.vehium.com
                          │
                    Load Balancer + TLS
                          │
         ┌────────────────┼────────────────┐
         │                │                │
    ┌─────────┐     ┌─────────┐     ┌─────────┐
    │  Node 1 │     │  Node 2 │     │  Node 3 │
    │  16 GB  │     │  16 GB  │     │  16 GB  │
    │ MASTER  │     │ MASTER  │     │ MASTER  │
    │  DB PRI │◄───►│  DB REP │◄───►│  DB REP │
    └─────────┘     └─────────┘     └─────────┘
                          │
                  Daily Offsite Backups
```

### Tech Stack

| Layer | Technology |
|-------|-----------|
| Orchestration | Kubernetes — 3 master nodes, fully HA |
| Database | PostgreSQL — streaming replication across all nodes |
| Storage | Replicated block storage (3x) for file uploads |
| Ingress | Reverse proxy with automatic TLS certificates |
| Deployment | GitOps — git push to deploy |
| Backups | Daily automated offsite backups |
| Power | UPS-protected with graceful shutdown |

### Resilience

- **Node failure** — cluster maintains quorum, pods reschedule automatically, database promotes a replica within seconds
- **Power outage** — UPS triggers graceful shutdown, all nodes auto-restart when power returns
- **Disk failure** — replicated storage serves data from remaining nodes and self-heals
- **Full cluster loss** — complete rebuild from offsite backups in ~2-4 hours

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
