# 🎯 Visualizzer — Enterprise Attack Path & Recon Graph Visualizer

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.10%2B-3776AB?style=for-the-badge&logo=python&logoColor=white" alt="Python 3.10+"/>
  <img src="https://img.shields.io/badge/Graph_Engine-NetworkX-008080?style=for-the-badge" alt="NetworkX"/>
  <img src="https://img.shields.io/badge/Visualization-PyVis-FF6F61?style=for-the-badge" alt="PyVis"/>
  <img src="https://img.shields.io/badge/Database-Neo4j-008CC1?style=for-the-badge&logo=neo4j&logoColor=white" alt="Neo4j"/>
  <img src="https://img.shields.io/badge/License-MIT-4BC51D?style=for-the-badge" alt="MIT License"/>
  <img src="https://img.shields.io/badge/Domain-Cybersecurity%20%2F%20Red%20Team-red?style=for-the-badge" alt="Red Team Tool"/>
</p>

<p align="center">
  <b>Visualizzer</b> is a high-performance threat analysis and attack path visualization engine. It seamlessly ingests heterogeneous recon data (Nmap XML, BloodHound JSONs, raw LDAP dumps), merges entity relationships into a unified <b>directed multigraph</b>, analyzes privilege escalation paths, and renders interactive HTML visualizations.
</p>

---

## 📑 Table of Contents

- [Key Features](#-key-features)
- [Architecture & Data Pipeline](#-architecture--data-pipeline)
- [Directory Layout](#-directory-layout)
- [Installation](#-installation)
- [Quick Start](#-quick-start)
- [Command Line Interface (CLI)](#-command-line-interface-cli)
- [Color Palette & Node Legend](#-color-palette--node-legend)
- [Path Analysis Capabilities](#-path-analysis-capabilities)
- [Configuration](#-configuration)
- [Neo4j Integration](#-neo4j-integration)
- [Contributing](#-contributing)
- [License](#-license)

---

## ✨ Key Features

- 🔀 **Multi-Source Data Fusion**: Simultaneously ingests and cross-correlates data from:
  - **Nmap XML/JSON**: Hosts, open ports, OS identification, and running services.
  - **BloodHound JSONs**: Active Directory objects (`Users`, `Groups`, `Computers`, `Domains`) and relations (`AdminTo`, `MemberOf`, `HasSession`, `GenericAll`, `WriteDacl`, etc.).
  - **LDAP Dumps**: Custom attribute mappings, Kerberoastable SPNs, and Unconstrained Delegation flags.
- 🧠 **Intelligent Node Merging**: Automatically resolves entity collisions (e.g., mapping IP `192.168.1.10` from Nmap to `DC01.CORP.LOCAL` from BloodHound) without losing attribute fidelity.
- ⚡ **Pathfinding & Risk Scoring Engine**:
  - Finds the shortest attack paths from unprivileged compromise points to **High Value Targets (HVTs)** / Domain Admins.
  - Highlights Kerberoastable accounts linked to Administrative privileges.
  - Detects Unconstrained Delegation host vulnerabilities coupled with active admin sessions.
- 🎨 **Interactive Physics-Driven HTML Graphs**: Built with **PyVis** — featuring node filtering, search, interactive drag-and-drop physics, and detailed metadata hover tooltips.
- 🗄️ **Neo4j Export**: Option to export Cypher queries or directly push the correlated graph to a live Neo4j database instance.
- 🧪 **Mock Data Generator Included**: Built-in utility to generate realistic synthetic Active Directory and Nmap scan datasets for offline testing and demos.

---

## 🏗️ Architecture & Data Pipeline
