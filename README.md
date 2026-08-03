  --bloodhound bloodhound/ \
  -o output/combined.html
```
### Export to Neo4j (Cypher script)
```bash
python main.py \
  --nmap mock_data/nmap_scan.xml \
  --bloodhound mock_data/bloodhound \
  --neo4j-cypher output/graph.cypher
```
### Direct Neo4j ingestion
```bash
python main.py \
  --nmap mock_data/nmap_scan.xml \
  --bloodhound mock_data/bloodhound \
  --neo4j-ingest \
  --neo4j-uri bolt://localhost:7687 \
  --neo4j-user neo4j \
  --neo4j-password your_password
```
---
## Supported Input Formats
### Nmap
| Format | Flag | Extracts |
|---|---|---|
| XML (`.xml`) | `--nmap` | Hosts, open ports, OS fingerprint, services |
| JSON (`.json`) | `--nmap` | Same fields from JSON scan output |
### BloodHound
| Files | Flag | Extracts |
|---|---|---|
| `users.json`, `computers.json`, `groups.json`, `domains.json` | `--bloodhound` | AD objects, ACLs, sessions, trust relationships |
**Supported edge types:** `AdminTo`, `MemberOf`, `HasSession`, `GenericAll`, `WriteDacl`, `WriteOwner`, `GenericWrite`, `ForceChangePassword`, `AllowedToDelegate`, and more.
### LDAP
| Format | Flag | Extracts |
|---|---|---|
| Custom JSON export (`.json`) | `--ldap` | Users, computers, groups, SPNs, delegation flags, group memberships |
---
## Analysis Engine
APV automatically detects:
| Vector | What it finds |
|---|---|
| **Shortest PrivEsc paths** | Routes from low-privileged users to Domain Admins / HVTs |
| **Kerberoast + AdminTo** | Kerberoastable service accounts with local admin rights |
| **Unconstrained delegation** | Computers with unconstrained delegation and active user sessions |
| **Sensitive port exposure** | Ports 3389, 445, 5985, etc. on hosts where a user has admin rights |
Results are printed to the console and optionally saved as JSON via `--analysis-report`.
---
## Visualization Legend
The interactive HTML graph uses risk-based styling:
| Color | Node Type |
|---|---|
| 🔴 `#FF4D4D` | High Value Targets (Domain Admins, Enterprise Admins) |
| 🟠 `#FF9933` | Kerberoastable users |
| 🟢 `#4DFF88` | Standard users |
| 🔵 `#4D94FF` | Computers / network devices |
| 🟣 `#B366FF` | Groups |
| 🟡 `#FFD94D` | Domains |
| Edge Style | Meaning |
|---|---|
| **Bold red arrow** | High-risk ACL (`GenericAll`, `WriteDacl`, `AdminTo`) |
| Gray arrow | Standard relationship (`MemberOf`, `Contains`) |
| Blue arrow | Active session (`HasSession`) |
**Interactive controls:** physics simulation, node search/filter, hover tooltips (ports, SPNs, group memberships), zoom & pan.
---
## CLI Reference
```
usage: apv [-h] [--nmap FILE] [--bloodhound DIR|FILE] [--ldap FILE]
           [-o FILE] [--analysis-report FILE] [--no-highlight]
           [--neo4j-cypher FILE] [--neo4j-ingest]
           [--neo4j-uri URI] [--neo4j-user USER] [--neo4j-password PASS]
           [--neo4j-database DB] [--log-level LEVEL] [--max-paths N]
Input Sources:
  --nmap FILE           Nmap XML or JSON scan file (repeatable)
  --bloodhound PATH     BloodHound JSON directory or file (repeatable)
  --ldap FILE           LDAP JSON export file (repeatable)
Output:
  -o, --output FILE     PyVis HTML output (default: attack_path_graph.html)
  --analysis-report     Save analysis results as JSON
  --no-highlight        Disable attack-path highlighting in graph
Neo4j Export:
  --neo4j-cypher FILE   Export graph as Cypher script
  --neo4j-ingest        Directly ingest into Neo4j database
General:
  --log-level           DEBUG | INFO | WARNING | ERROR
  --max-paths N         Max privilege escalation paths to report (default: 50)
```
---
## Project Structure
```
Visualizzer/
├── main.py                      # CLI entry point
├── config.py                    # Colors, shapes, constants
├── models.py                    # Data classes (GraphNode, GraphEdge, etc.)
├── generate_mock_data.py        # Demo data generator
├── requirements.txt
├── parsers/
│   ├── nmap_parser.py           # Nmap XML/JSON parser
│   ├── bloodhound_parser.py     # BloodHound JSON parser
│   └── ldap_parser.py           # LDAP JSON parser
├── core/
│   ├── graph_builder.py         # Multi-source graph fusion
│   └── analyzer.py              # PrivEsc path analysis engine
└── visualizers/
    ├── pyvis_renderer.py        # Interactive HTML renderer
    └── neo4j_exporter.py        # Neo4j Cypher export & ingestion
```
---
## Example Output
Running against the included mock data produces:
```
Graph built: 11 nodes, 15 edges
High-value targets: 1 (DOMAIN ADMINS@CORP.LOCAL)
Privilege escalation paths: 1
Kerberoast + AdminTo risks: 2
Unconstrained delegation risks: 1
Sensitive port risks: 2
Interactive graph: output/attack_path_graph.html
```
---
## License
This project is intended for **authorized security assessments only**. Use responsibly and only on systems you have explicit permission to test.
---
<p align="center">
  <sub>Built with Python · NetworkX · PyVis · Neo4j</sub>
</p>
