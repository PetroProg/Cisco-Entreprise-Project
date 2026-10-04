# P_RES-129 – Multi-site Network Infrastructure (Saint-Cosme)

Design, deployment and securing of a multi-site network for the municipality of Saint-Cosme, simulated in **Cisco Packet Tracer**.

**Author:** Petro Maltsev

## Project overview

- 3 sites: administrative center (main building), media library and technical services (workshop)
- VLAN segmentation with centralized DHCP and inter-VLAN routing (L3 switch and sub-interfaces)
- Static routing between sites, fiber link with SFP
- Isolated visitors Wi-Fi (VLAN 99) restricted by an extended ACL
- Internet access filtered by a Cisco ASA firewall (NAT, inspection, ACL)

## Presentation

Open the class presentation (Marp Markdown): [Rapport/X-P_Res_129-Maltsev-Presentation.md](Rapport/X-P_Res_129-Maltsev-Presentation.md)

> Render it with the [Marp for VS Code](https://marketplace.visualstudio.com/items?itemName=marp-team.marp-vscode) extension or the Marp CLI.

## Project Structure & Files

| Folder / File | Description |
|---|---|
| **`Cahier des charges/`** | Specifications & requirements |
| ├─ `E_P_RES_129-CDC_GRP6.pdf` | Project specifications (Group 6) |
| └─ `H-P_RES-129_CDC.pdf` | Reference specifications |
| **`Solution/`** | Technical realization & architecture |
| ├─ `X-P_Res_129-Maltsev-Realisation.pkt` | Cisco Packet Tracer simulation |
| └─ `X-P_Res_129-Maltsev-Schema.drawio` | Editable network diagram |
| **`Rapport/`** | Documentation & presentation |
| ├─ `R-P_Res_129-Maltsev-Petro.md` | Final technical report |
| ├─ `X-P_Res_129-Maltsev-Presentation.md` | Slide deck presentation (Marp) |
| ├─ `X-P_Res_129-Maltsev-PlanAdressage.xlsx` | IP addressing plan spreadsheet |
| ├─ `X-P_Res_129-Maltsev-Schema.svg` | Vector network diagram |
| └─ `X-P_Res_129-Maltsev-topologie.png` | Packet Tracer topology screenshot |
| **Root** | |
| └─ `T-P_Res_129-Maltsev-JdT.md` | Work log (Journal de travail) |
