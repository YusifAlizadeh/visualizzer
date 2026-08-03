```python
readme_content = """# 🎯 Visualizzer — Enterprise Attack Path & Recon Graph Visualizer

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


```

┌───────────────────┐    ┌───────────────────┐    ┌───────────────────┐
│     Nmap XML      │    │  BloodHound JSON  │    │     LDAP JSON     │
└─────────┬─────────┘    └─────────┬─────────┘    └─────────┬─────────┘
│                        │                        │
▼                        ▼                        ▼
┌─────────────────────────────────────────────────────────────────────┐
│                            PARSER LAYER                             │
│     (nmap_parser.py | bloodhound_parser.py | ldap_parser.py)        │
└──────────────────────────────────┬──────────────────────────────────┘
│
▼
┌─────────────────────────────────────────────────────────────────────┐
│                   GRAPH BUILDER & ENTITY ENGINE                     │
│      Fuses nodes/edges into unified NetworkX DiGraph (core/)        │
└──────────────────────────────────┬──────────────────────────────────┘
│
▼
┌─────────────────────────────────────────────────────────────────────┐
│                         ANALYZER ENGINE                             │
│     Evaluates PrivEsc paths, HVTs, Delegation & Open Ports (core/)   │
└─────────┬────────────────────────┴────────────────────────┬─────────┘
│                                                 │
▼                                                 ▼
┌───────────────────┐                             ┌───────────────────┐
│  PyVis Renderer   │                             │  Neo4j Exporter   │
│ (Interactive HTML)│                             │  (Cypher / Bolt)  │
└───────────────────┘                             └───────────────────┘

```

---

## 📁 Directory Layout


```

visualizzer/
├── config.py                 # Global styles, colors, physics, and default parameters
├── models.py                 # Dataclasses for Nodes (User, Group, Computer) & Edges
├── parsers/
│   ├── **init**.py
│   ├── nmap_parser.py        # Parses Nmap XML scans for open ports and services
│   ├── bloodhound_parser.py  # Parses BloodHound zip/json export files
│   └── ldap_parser.py        # Parses custom LDAP json/dict attributes
├── core/
│   ├── **init**.py
│   ├── graph_builder.py      # Entity resolution, graph fusion, and NetworkX logic
│   └── analyzer.py           # PrivEsc algorithms, shortest path, Kerberoast detection
├── visualizers/
│   ├── **init**.py
│   ├── pyvis_renderer.py     # HTML canvas graph generator with custom CSS/tooltips
│   └── neo4j_exporter.py     # Cypher query builder & Neo4j database ingestor
├── generate_mock_data.py     # Utility script to generate dummy AD/Nmap test data
├── main.py                   # CLI entry point (argparse & logging)
├── requirements.txt          # Python dependencies
└── README.md                 # Project documentation

```

---

## ⚙️ Installation

1. **Clone the repository**:
   ```bash
   git clone [https://github.com/your-org/visualizzer.git](https://github.com/your-org/visualizzer.git)
   cd visualizzer

```

2. **Create a virtual environment**:
```bash
python3 -m venv venv
source venv/bin/activate  # On Windows use: venv\\Scripts\\activate

```


3. **Install dependencies**:
```bash
pip install -r requirements.txt

```



---

## 🚀 Quick Start

### 1. Generate Synthetic Test Data

If you don't have active scan files available, generate mock datasets instantly:

```bash
python generate_mock_data.py --output-dir ./sample_data

```

### 2. Run Visualizzer Engine

Analyze and generate an interactive attack path visualization:

```bash
python main.py \
  --bloodhound ./sample_data/bh_data/ \
  --nmap ./sample_data/nmap_scan.xml \
  --ldap ./sample_data/ldap_export.json \
  --output attack_graph.html

```

### 3. Open Visualization

Open the generated `attack_graph.html` in any web browser to explore interactive nodes, privilege paths, and tooltips!

---

## 💻 Command Line Interface (CLI)

```bash
usage: main.py [-h] [--bloodhound DIR] [--nmap FILE] [--ldap FILE]
               [--output FILE] [--neo4j-uri URI] [--neo4j-user USER]
               [--neo4j-pass PASS] [--analyze-target HVT_NAME] [--verbose]

```

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `--bloodhound` | Path | `None` | Path to directory containing BloodHound JSONs (`users.json`, `computers.json`, etc.) |
| `--nmap` | Path | `None` | Path to Nmap XML output scan file |
| `--ldap` | Path | `None` | Path to raw LDAP JSON export file |
| `--output` | Path | `attack_graph.html` | Output HTML graph file name |
| `--neo4j-uri` | String | `None` | Neo4j Bolt connection URI (e.g., `bolt://localhost:7687`) |
| `--neo4j-user` | String | `neo4j` | Neo4j database user |
| `--neo4j-pass` | String | `None` | Neo4j database password |
| `--analyze-target` | String | `Domain Admins` | Specific target node name to calculate attack paths against |
| `--verbose` | Flag | `False` | Enable detailed debug log output |

---

## 🎨 Color Palette & Node Legend

Visualizzer employs an intuitive security-first visual coding scheme:

| Entity Type | Shape | Hex Color | Description / Example |
| --- | --- | --- | --- |
| **High Value Target** | 🛑 Diamond / Box | `#FF4D4D` | Domain Admins, Enterprise Admins, Key Domain Controllers |
| **Kerberoastable User** | 🟧 Circle | `#FFA500` | Accounts with SPNs set & high privileges |
| **Standard User** | 🟩 Circle | `#4DFF88` | Standard domain user accounts |
| **Computer / Host** | 🟦 Laptop / Server | `#4D94FF` | Domain Workstations, Servers, & Network Devices |
| **Group / OU** | 🟪 Folder | `#B366FF` | Active Directory Security Groups |
| **Service / Port** | ⚪ Dot | `#E0E0E0` | Discovered open ports/services (e.g., RDP:3389, SMB:445) |

### Relationship Edges

* 🔴 **Bold Red Directed Line**: Critical Abuse Paths (`GenericAll`, `WriteDacl`, `AllExtendedRights`, `AdminTo`).
* 🟠 **Orange Line**: Session / Delegation vectors (`HasSession`, `AllowedToDelegate`).
* 🔵 **Blue Line**: Standard Group Memberships (`MemberOf`).

---

## 🔍 Path Analysis Capabilities

Visualizzer automatically executes heuristic analysis routines over the NetworkX graph:

1. **Shortest Attack Vector**: Computes the shortest path from any non-privileged user node to Domain Admin targets using Dijkstra's weighted algorithm.
2. **Kerberoastable PrivEsc Vectors**: Flags user accounts with set SPNs that possess administrative or control relationships over critical host computers.
3. **Unconstrained Delegation Paths**: Identifies computers configured with unconstrained delegation that host active privileged user sessions.
4. **Exposed Administrative Interfaces**: Maps local admin rights directly against open remote management ports (`3389 RDP`, `5985 WinRM`, `445 SMB`, `22 SSH`).

---

## ⚙️ Configuration

Custom colors, graph physics, and thresholds can be tuned in `config.py`:

```python
# config.py - Excerpt
COLOR_PALETTE = {
    "HVT": "#FF4D4D",
    "KERBEROASTABLE": "#FFA500",
    "USER": "#4DFF88",
    "COMPUTER": "#4D94FF",
    "GROUP": "#B366FF",
    "SERVICE": "#CCCCCC"
}

PYVIS_PHYSICS_OPTIONS = """
{
  "physics": {
    "barnesHut": {
      "gravitationalConstant": -8000,
      "centralGravity": 0.3,
      "springLength": 95
    },
    "minVelocity": 0.75
  }
}
"""

```

---

## 🗄️ Neo4j Integration

To sync your fused graph directly into a Neo4j database instance:

```bash
python main.py \
  --bloodhound ./sample_data/bh_data/ \
  --nmap ./sample_data/nmap_scan.xml \
  --neo4j-uri bolt://localhost:7687 \
  --neo4j-user neo4j \
  --neo4j-pass MySecretPassword

```

Once synced, you can execute complex Cypher queries directly in Neo4j Browser:

```cypher
// Find all Kerberoastable users with direct admin rights to DC
MATCH p=(u:User {is_kerberoastable: true})-[r:AdminTo]->(c:Computer {is_hvt: true})
RETURN p

```

---

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository.
2. Create a feature branch (`git checkout -b feature/AmazingFeature`).
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`).
4. Push to the branch (`git push origin feature/AmazingFeature`).
5. Open a Pull Request.

---

## 📄 License

Distributed under the **MIT License**. See `LICENSE` for more information.

with open("README.md", "w", encoding="utf-8") as f:
f.write(readme_content)

print("File README.md successfully created!")

```

```text?code_stdout&code_event_index=1
File README.md successfully created!


```

Your README file for **Visualizzer** is ready


---

### 📖 README Overview & Preview

Here is a full preview of the generated **`README.md`** file for your project:

```markdown
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


```

┌───────────────────┐    ┌───────────────────┐    ┌───────────────────┐
│     Nmap XML      │    │  BloodHound JSON  │    │     LDAP JSON     │
└─────────┬─────────┘    └─────────┬─────────┘    └─────────┬─────────┘
│                        │                        │
▼                        ▼                        ▼
┌─────────────────────────────────────────────────────────────────────┐
│                            PARSER LAYER                             │
│     (nmap_parser.py | bloodhound_parser.py | ldap_parser.py)        │
└──────────────────────────────────┬──────────────────────────────────┘
│
▼
┌─────────────────────────────────────────────────────────────────────┐
│                   GRAPH BUILDER & ENTITY ENGINE                     │
│      Fuses nodes/edges into unified NetworkX DiGraph (core/)        │
└──────────────────────────────────┬──────────────────────────────────┘
│
▼
┌─────────────────────────────────────────────────────────────────────┐
│                         ANALYZER ENGINE                             │
│     Evaluates PrivEsc paths, HVTs, Delegation & Open Ports (core/)   │
└─────────┬────────────────────────┴────────────────────────┬─────────┘
│                                                 │
▼                                                 ▼
┌───────────────────┐                             ┌───────────────────┐
│  PyVis Renderer   │                             │  Neo4j Exporter   │
│ (Interactive HTML)│                             │  (Cypher / Bolt)  │
└───────────────────┘                             └───────────────────┘

```

---

## 📁 Directory Layout


```

visualizzer/
├── config.py                 # Global styles, colors, physics, and default parameters
├── models.py                 # Dataclasses for Nodes (User, Group, Computer) & Edges
├── parsers/
│   ├── **init**.py
│   ├── nmap_parser.py        # Parses Nmap XML scans for open ports and services
│   ├── bloodhound_parser.py  # Parses BloodHound zip/json export files
│   └── ldap_parser.py        # Parses custom LDAP json/dict attributes
├── core/
│   ├── **init**.py
│   ├── graph_builder.py      # Entity resolution, graph fusion, and NetworkX logic
│   └── analyzer.py           # PrivEsc algorithms, shortest path, Kerberoast detection
├── visualizers/
│   ├── **init**.py
│   ├── pyvis_renderer.py     # HTML canvas graph generator with custom CSS/tooltips
│   └── neo4j_exporter.py     # Cypher query builder & Neo4j database ingestor
├── generate_mock_data.py     # Utility script to generate dummy AD/Nmap test data
├── main.py                   # CLI entry point (argparse & logging)
├── requirements.txt          # Python dependencies
└── README.md                 # Project documentation

```

---

## ⚙️ Installation

1. **Clone the repository**:
   ```bash
   git clone [https://github.com/your-org/visualizzer.git](https://github.com/your-org/visualizzer.git)
   cd visualizzer

```

2. **Create a virtual environment**:
```bash
python3 -m venv venv
source venv/bin/activate  # On Windows use: venv\Scripts\activate

```


3. **Install dependencies**:
```bash
pip install -r requirements.txt

```



---

## 🚀 Quick Start

### 1. Generate Synthetic Test Data

If you don't have active scan files available, generate mock datasets instantly:

```bash
python generate_mock_data.py --output-dir ./sample_data

```

### 2. Run Visualizzer Engine

Analyze and generate an interactive attack path visualization:

```bash
python main.py \
  --bloodhound ./sample_data/bh_data/ \
  --nmap ./sample_data/nmap_scan.xml \
  --ldap ./sample_data/ldap_export.json \
  --output attack_graph.html

```

### 3. Open Visualization

Open the generated `attack_graph.html` in any web browser to explore interactive nodes, privilege paths, and tooltips!

---

## 💻 Command Line Interface (CLI)

```bash
usage: main.py [-h] [--bloodhound DIR] [--nmap FILE] [--ldap FILE]
               [--output FILE] [--neo4j-uri URI] [--neo4j-user USER]
               [--neo4j-pass PASS] [--analyze-target HVT_NAME] [--verbose]

```

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `--bloodhound` | Path | `None` | Path to directory containing BloodHound JSONs (`users.json`, `computers.json`, etc.) |
| `--nmap` | Path | `None` | Path to Nmap XML output scan file |
| `--ldap` | Path | `None` | Path to raw LDAP JSON export file |
| `--output` | Path | `attack_graph.html` | Output HTML graph file name |
| `--neo4j-uri` | String | `None` | Neo4j Bolt connection URI (e.g., `bolt://localhost:7687`) |
| `--neo4j-user` | String | `neo4j` | Neo4j database user |
| `--neo4j-pass` | String | `None` | Neo4j database password |
| `--analyze-target` | String | `Domain Admins` | Specific target node name to calculate attack paths against |
| `--verbose` | Flag | `False` | Enable detailed debug log output |

---

## 🎨 Color Palette & Node Legend

Visualizzer employs an intuitive security-first visual coding scheme:

| Entity Type | Shape | Hex Color | Description / Example |
| --- | --- | --- | --- |
| **High Value Target** | 🛑 Diamond / Box | `#FF4D4D` | Domain Admins, Enterprise Admins, Key Domain Controllers |
| **Kerberoastable User** | 🟧 Circle | `#FFA500` | Accounts with SPNs set & high privileges |
| **Standard User** | 🟩 Circle | `#4DFF88` | Standard domain user accounts |
| **Computer / Host** | 🟦 Laptop / Server | `#4D94FF` | Domain Workstations, Servers, & Network Devices |
| **Group / OU** | 🟪 Folder | `#B366FF` | Active Directory Security Groups |
| **Service / Port** | ⚪ Dot | `#E0E0E0` | Discovered open ports/services (e.g., RDP:3389, SMB:445) |

### Relationship Edges

* 🔴 **Bold Red Directed Line**: Critical Abuse Paths (`GenericAll`, `WriteDacl`, `AllExtendedRights`, `AdminTo`).
* 🟠 **Orange Line**: Session / Delegation vectors (`HasSession`, `AllowedToDelegate`).
* 🔵 **Blue Line**: Standard Group Memberships (`MemberOf`).

---

## 🔍 Path Analysis Capabilities

Visualizzer automatically executes heuristic analysis routines over the NetworkX graph:

1. **Shortest Attack Vector**: Computes the shortest path from any non-privileged user node to Domain Admin targets using Dijkstra's weighted algorithm.
2. **Kerberoastable PrivEsc Vectors**: Flags user accounts with set SPNs that possess administrative or control relationships over critical host computers.
3. **Unconstrained Delegation Paths**: Identifies computers configured with unconstrained delegation that host active privileged user sessions.
4. **Exposed Administrative Interfaces**: Maps local admin rights directly against open remote management ports (`3389 RDP`, `5985 WinRM`, `445 SMB`, `22 SSH`).

---

## ⚙️ Configuration

Custom colors, graph physics, and thresholds can be tuned in `config.py`:

```python
# config.py - Excerpt
COLOR_PALETTE = {
    "HVT": "#FF4D4D",
    "KERBEROASTABLE": "#FFA500",
    "USER": "#4DFF88",
    "COMPUTER": "#4D94FF",
    "GROUP": "#B366FF",
    "SERVICE": "#CCCCCC"
}

PYVIS_PHYSICS_OPTIONS = """
{
  "physics": {
    "barnesHut": {
      "gravitationalConstant": -8000,
      "centralGravity": 0.3,
      "springLength": 95
    },
    "minVelocity": 0.75
  }
}
"""

```

---

## 🗄️ Neo4j Integration

To sync your fused graph directly into a Neo4j database instance:

```bash
python main.py \
  --bloodhound ./sample_data/bh_data/ \
  --nmap ./sample_data/nmap_scan.xml \
  --neo4j-uri bolt://localhost:7687 \
  --neo4j-user neo4j \
  --neo4j-pass MySecretPassword

```

Once synced, you can execute complex Cypher queries directly in Neo4j Browser:

```cypher
// Find all Kerberoastable users with direct admin rights to DC
MATCH p=(u:User {is_kerberoastable: true})-[r:AdminTo]->(c:Computer {is_hvt: true})
RETURN p

```

---

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository.
2. Create a feature branch (`git checkout -b feature/AmazingFeature`).
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`).
4. Push to the branch (`git push origin feature/AmazingFeature`).
5. Open a Pull Request.

---

## 📄 License

Distributed under the **MIT License**. See `LICENSE` for more information.
