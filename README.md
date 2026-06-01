<div align="center">
  <a href="https://www.nttdata.com" target="_blank">
    <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/0/09/NTT-Data-Logo.svg/3840px-NTT-Data-Logo.svg.png" alt="NTT DATA" width="280"/>
  </a>

  <h1>Private 5G Helm Chart Repository</h1>

  <p>
    Custom Helm Charts maintained by <strong>NTT DATA</strong> to showcase the capabilities of
    <a href="https://osm.etsi.org" target="_blank">OSM (Open Source MANO)</a> in live demo scenarios.
  </p>

  <p>
    <img src="https://img.shields.io/badge/Helm-v3-0F1689?logo=helm&logoColor=white" alt="Helm v3"/>
    <img src="https://img.shields.io/badge/5G-SA%20Core-00B2FF?logo=5g&logoColor=white" alt="5G SA"/>
    <img src="https://img.shields.io/badge/OSM-Open%20Source%20MANO-E30613?logo=etsi&logoColor=white" alt="OSM"/>
    <img src="https://img.shields.io/badge/Kubernetes-Ready-326CE5?logo=kubernetes&logoColor=white" alt="Kubernetes"/>
  </p>
</div>

---

## 📋 Overview

This repository is a **Helm chart repository** (served via GitHub Pages) that hosts the custom Helm Charts developed and maintained by NTT DATA. These charts are the backbone of a controlled 5G demo environment orchestrated by **OSM (Open Source MANO)**, demonstrating end-to-end private 5G network deployment capabilities.

The charts are consumed by OSM packages published at:
> 🔗 [osm-packages / demoeucnc-2026 / oka/apps](https://osm.etsi.org/gitlab/vnf-onboarding/osm-packages/-/tree/demoeucnc-2026/oka/apps?ref_type=heads)

---

## 🏗️ Architecture

The demo deploys a **fully functional, cloud-native private 5G network** on Kubernetes, covering both the Core Network and the Radio Access Network (RAN) simulation layer. Two 5G core implementations are supported:

| Layer              | Technology                                          | Description                                         |
| ------------------ | --------------------------------------------------- | --------------------------------------------------- |
| **5G Core**        | [free5GC](https://free5gc.org)                      | Open-source 5GC implementation (3GPP Release 15/16) |
| **5G Core**        | [OpenAirInterface](https://openairinterface.org)    | OAI 5G Release 16 Core Network                      |
| **RAN Simulation** | [UERANSIM](https://github.com/aligungr/UERANSIM)    | UE and gNB simulator for free5GC                    |
| **RAN Simulation** | [MobSim](https://gitlab.eurecom.fr/gnanasek/mobsim) | 5G mobility and traffic simulator                   |
| **RAN**            | OAI gNB / NR-UE                                     | Software-defined gNB and UE via OpenAirInterface    |

---

## 📦 Available Helm Charts

### free5GC Stack

| Chart                | Version | App Version | Description                                                                                     |
| -------------------- | ------- | ----------- | ----------------------------------------------------------------------------------------------- |
| `free5gc-core`       | v4.2.2  | v4.2.2      | Complete free5GC core (AMF, SMF, NRF, UPF, UDM, UDR, AUSF, CHF, NEF, NSSF, PCF, WebUI, MongoDB) |
| `free5gc-single-upf` | v4.2.2  | v4.2.2      | free5GC core with a single dedicated UPF instance                                               |
| `free5gc-ueransim`   | v2.0.17 | v4.0.1      | UERANSIM-based UE and gNB simulator connected to free5GC                                        |

### OpenAirInterface Stack

| Chart          | Version | App Version | Description                                                           |
| -------------- | ------- | ----------- | --------------------------------------------------------------------- |
| `oai-5g-core`  | v2.2.1  | v2.2.1      | OAI 5G Release 16 Core (NRF, UDR, UDM, AUSF, AMF, SMF, UPF, LMF, IMS) |
| `oai-5g-gnb`   | v2.4.0  | 2026.w15    | OAI software-defined gNB (supports RF Simulator, USRP B2xx/N3xx)      |
| `oai-5g-nr-ue` | v2.4.0  | 2026.w15    | OAI NR-UE (supports RF Simulator and USRP hardware)                   |

### Simulation & Tooling

| Chart    | Version | App Version | Description                                           |
| -------- | ------- | ----------- | ----------------------------------------------------- |
| `mobsim` | 0.1.1   | 1.0.0       | MobSim 5G simulator — gNBs, UEs, and simulator engine |

---

## 🚀 Using this Helm Repository

Add this repository to your Helm client:

```bash
helm repo add nttdata-5g https://alesan121.github.io/free5gc-helm-repo
helm repo update
```

Search for available charts:

```bash
helm search repo nttdata-5g
```

Install a chart (example with free5GC core):

```bash
helm install free5gc nttdata-5g/free5gc-core \
  --namespace free5gc \
  --create-namespace
```

---

## 🎯 OSM Integration

These charts are packaged as **OSM Application Packages** and deployed through OSM's built-in Kubernetes application management. The OSM packages in the [`demoeucnc-2026`](https://osm.etsi.org/gitlab/vnf-onboarding/osm-packages/-/tree/demoeucnc-2026/oka/apps?ref_type=heads) branch include:

| OSM Package    | Helm Chart Used      | Purpose                                          |
| -------------- | -------------------- | ------------------------------------------------ |
| `free5gc-core` | `free5gc-core`       | Deploy the full free5GC control & user plane     |
| `free5gc-upf`  | `free5gc-single-upf` | Deploy a standalone UPF for multi-site scenarios |
| `ueransim`     | `free5gc-ueransim`   | Simulate UEs and gNBs connecting to free5GC      |
| `oai-5g-core`  | `oai-5g-core`        | Deploy the OAI 5G core as an alternative CN      |
| `mobsim`       | `mobsim`             | Run mobility and traffic simulation workloads    |

---

## 🧪 Demo Scenario

The demo showcases a **controlled private 5G network** deployed end-to-end:

```
┌─────────────────────────────────────────────────────────┐
│                    Kubernetes Cluster                   │
│                                                         │
│  ┌──────────────┐     ┌──────────────────────────────┐  │
│  │   RAN Layer  │     │        5G Core (CN)          │  │
│  │              │     │                              │  │
│  │  gNB (sim)  ◄──────►  AMF  SMF  NRF  UPF  ...     │  │
│  │  UE  (sim)   │     │  UDM  UDR  AUSF  PCF  ...    │  │
│  │  MobSim      │     │  MongoDB  WebUI              │  │
│  └──────────────┘     └──────────────────────────────┘  │
│            ▲                      ▲                     │
│            └──────────────────────┘                     │
│                   OSM orchestrates                      │
└─────────────────────────────────────────────────────────┘
```

OSM manages the **lifecycle** of every component — instantiation, scaling, healing, and termination — using these Helm charts under the hood.

---

## 👥 Maintainers

This repository is maintained by the **NTT DATA** team. For questions or contributions, please open an issue or a pull request.

<div align="center">
  <a href="https://www.nttdata.com" target="_blank">
    <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/0/09/NTT-Data-Logo.svg/3840px-NTT-Data-Logo.svg.png" alt="NTT DATA" width="200"/>
  </a>
  <br/>
  <sub>© NTT DATA — Making the world more sustainable and secure through innovation</sub>
</div>

---

## 📄 License

This project is distributed under the terms of the applicable open-source licenses of each upstream project (free5GC, OpenAirInterface, UERANSIM). Custom NTT DATA additions are released under the [Apache 2.0 License](LICENSE).
