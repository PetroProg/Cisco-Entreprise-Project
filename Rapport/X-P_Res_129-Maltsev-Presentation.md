---
marp: true
theme: default
paginate: true
size: 16:9
backgroundColor: '#0f172a'
color: '#f8fafc'
style: |
  section {
    font-family: "Segoe UI", Arial, sans-serif;
    padding: 42px 58px 38px;
    border-top: 8px solid #2563eb;
    background: #0f172a;
    color: #f8fafc;
  }
  section.title {
    background: linear-gradient(135deg, #0f172a 0%, #1e293b 100%);
    color: #f8fafc;
    border-top-color: #38bdf8;
    padding: 90px 80px;
  }
  section.title h1 { font-size: 40px; color: #f8fafc; }
  section.full { padding: 10px; display: flex; justify-content: center; align-items: center; }
  section.full img { background: #f8fafc; border-radius: 8px; }
  section.title h2 { font-size: 23px; color: #94a3b8; font-weight: 400; }
  h1 { color: #f8fafc; font-size: 30px; margin-bottom: 8px; }
  h2 { color: #94a3b8; font-size: 18px; font-weight: 400; margin-top: 0; }
  h3 { color: #0f172a; font-size: 20px; margin: 4px 0 10px; }
  .tag { color: #38bdf8; border: 1px solid #38bdf8; border-radius: 18px; padding: 6px 14px; font-weight: 600; }
  .grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 18px; }
  .grid3 { display: grid; grid-template-columns: repeat(3, 1fr); gap: 16px; }
  .card { background: #1e293b; border: 1px solid #475569; border-radius: 12px; padding: 15px 18px; color: #e2e8f0; }
  .card h3 { color: #f8fafc; }
  .card code { color: #f9a8d4; }
  .card strong { color: #7dd3fc; }
  .note { background: #172554; border: 1px solid #2563eb; border-radius: 8px; padding: 10px 14px; color: #bfdbfe; }
  table { display: table; width: 100%; font-size: 16px; background: transparent; color: #e2e8f0; }
  tr { background: #1e293b !important; }
  tr:nth-child(2n) { background: #334155 !important; }
  th { background: #0f172a; color: white; }
  td { color: #e2e8f0; border-color: #475569; }
  code { color: #f9a8d4; }
  pre { background: #0f172a !important; color: #f8fafc; font-size: 13px; line-height: 1.25; }
---

<!-- _class: title -->
<!-- _backgroundColor: #0f172a -->
<!-- _color: #f8fafc -->

<span class="tag" style="color:#38bdf8;border-color:#38bdf8;">P_RES-129 · Cisco Packet Tracer</span>

<h1 style="color:#f8fafc;">Déploiement et sécurisation<br>d’une infrastructure réseau multi-sites</h1>

<h2 style="color:#cbd5e1;">Commune de Saint-Cosme — Présentation du projet</h2>

<br>

<div style="color:#f8fafc;">
<strong>Auteur :</strong> Petro Maltsev<br>
<strong>Outil de simulation :</strong> Cisco Packet Tracer<br>
<strong>Date :</strong> 05.10.2026
</div>

---

# Description du projet et exigences
## Trois bâtiments à interconnecter

| Bâtiment | Postes fixes | Postes publics | Imprimantes réseau | Bornes Wi-Fi | Distance |
|---|---:|---:|---:|---:|---:|
| Centre administratif (principal, 3 niveaux + sous-sol) | 70 | 0 | 4 | 0 | — |
| Médiathèque (secondaire) | 7 | 14 | 1 | 3 | 300 m |
| Services techniques (atelier) | 12 | 0 | 0 | 0 | 150 m |

<div class="note"><strong>Périmètre :</strong> un bâtiment principal et deux bâtiments secondaires, tous reliés au réseau du centre administratif.</div>

---

# Description du projet (suite)
## Détail du bâtiment principal : le Centre administratif

Les services de la commune sont répartis par étage. Chaque étage dispose d’un local de brassage relié au local IT du sous-sol.

| Niveau | Services et équipements | Postes fixes | Imprimantes |
|---|---|---:|---:|
| Sous-sol | Local IT : baie principale, 2 serveurs internes | — | — |
| Rez-de-chaussée | Guichets et service social | 28 | 2 |
| 1er étage | Urbanisme et aménagement | 20 | 1 |
| 2e étage | Finances et direction | 22 | 1 |

<div class="note"><strong>Exigence :</strong> les serveurs du sous-sol doivent être accessibles depuis tous les bâtiments ; chaque bâtiment secondaire dispose d’un local technique.</div>

---

<!-- _class: full -->

![h:690](X-P_Res_129-Maltsev-Schema.svg)

---

<!-- _class: full -->

![h:690](X-P_Res_129-Maltsev-topologie.png)

---

# Plan d’adressage
## VLAN et liaisons WAN

<style scoped>
table { font-size: 13px; }
th, td { padding: 3px 8px; }
</style>

| Nom VLAN | N° VLAN | Adresse IP | Broadcast | CIDR | Masque | Passerelle | IPs réservées et statiques |
|---|---|---|---|---|---|---|---|
| VLAN_FIN_DIR | VLAN 40 | 172.31.14.0 | 172.31.14.255 | /24 | 255.255.255.0 | 172.31.14.1 | 172.31.14.9 (Imprimante) |
| VLAN_GUICHETS | VLAN 20 | 172.31.11.0 | 172.31.11.255 | /24 | 255.255.255.0 | 172.31.11.1 | 172.31.11.8 et 172.31.11.9 (Imprimante) |
| VLAN_URB_AM | VLAN 30 | 172.31.12.0 | 172.31.12.255 | /24 | 255.255.255.0 | 172.31.12.1 | 172.31.12.9 (Imprimante) |
| VLAN_ADMIN | VLAN 10 | 172.31.1.0 | 172.31.1.15 | /28 | 255.255.255.240 | 172.31.1.1 | 172.31.1.2 et 172.31.1.3 (Serveurs) |
| VLAN_VISITE | VLAN 99 | 172.31.19.0 | 172.31.19.255 | /24 | 255.255.255.0 | 172.31.19.1 | - |
| VLAN_TECH | VLAN 50 | 172.31.13.0 | 172.31.13.255 | /24 | 255.255.255.0 | 172.31.13.1 | - |
| VLAN_FIXE | VLAN 60 | 172.31.15.0 | 172.31.15.255 | /24 | 255.255.255.0 | 172.31.15.1 | 172.31.15.9 (Imprimante) |
| VLAN_PUBLIC | VLAN 90 | 172.31.20.0 | 172.31.20.255 | /24 | 255.255.255.0 | 172.31.20.1 | - |
| VLAN_FIREWALL | VLAN 999 | 172.31.99.0 | 172.31.99.3 | /30 | 255.255.255.252 | 172.31.99.1 | 172.31.99.1 et 172.31.99.2 |

| WAN : équipement A | WAN : équipement B | Adresse IP |
|---|---|---|
| RT_ADMIN | RT_MEDIA | 172.31.254.0 /30 |
| RT_ADMIN | RT_TECH | 172.31.252.0 /30 |
| RT_ADMIN | SW_L3_ADMIN | 172.31.253.0 /30 |
| FW_ASA_1 | RT_INTERNET | 203.0.113.0 /30 |

---

# Pools DHCP
## Serveur DHCP centralisé (`SRV_DHCP`)

| Pool Name | Default Gateway | DNS Server | Start IP Address | Subnet Mask | Max User |
|---|---|---|---|---|---:|
| Pool_Media_Public | 172.31.20.1 | 172.31.1.3 | 172.31.20.10 | 255.255.255.0 | 40 |
| Pool_Guichets | 172.31.11.1 | 172.31.1.3 | 172.31.11.10 | 255.255.255.0 | 40 |
| Pool_Fin_Dir | 172.31.14.1 | 172.31.1.3 | 172.31.14.10 | 255.255.255.0 | 40 |
| Pool_URB_AM | 172.31.12.1 | 172.31.1.3 | 172.31.12.10 | 255.255.255.0 | 40 |
| Pool_Tech | 172.31.13.1 | 172.31.1.3 | 172.31.13.10 | 255.255.255.0 | 40 |
| Pool_Media | 172.31.15.1 | 172.31.1.3 | 172.31.15.10 | 255.255.255.0 | 40 |

---

# DHCP du VLAN 99 sur RT_MEDIA
## Le Wi-Fi visiteurs est servi localement par le routeur

```text
ip dhcp excluded-address 172.31.19.1
!
ip dhcp pool Pool_Visiteur
 network 172.31.19.0 255.255.255.0
 default-router 172.31.19.1
 dns-server 172.31.1.2
```

<div class="note"><strong>Pool_Visiteur :</strong> réseau <code>172.31.19.0/24</code>, passerelle <code>172.31.19.1</code> exclue de la distribution, DNS <code>172.31.1.2</code>.</div>

---

# Configuration des switchs L2
## Commandes universelles pour tous les commutateurs d’accès L2

```text
interface FastEthernet0/1
 switchport trunk allowed vlan 10,20,30,40,50,60,90
 switchport mode trunk

interface range FastEthernet0/2 - 4
 switchport access vlan 40
 switchport mode access
```

<div class="note"><strong>Universel :</strong> la même structure s’applique à tous les switchs L2 ; seuls les VLAN autorisés sur le trunk et le VLAN d’accès changent selon le site.</div>

---

# Configuration du switch L3
## Interface VLAN, routage et route par défaut

<div class="grid">
<div class="card">

### Configuration L2

```text
interface Vlan20
    ip address 172.31.11.1 255.255.255.0
    ip helper-address 172.31.1.3

interface range GigabitEthernet1/0/2 - 4
    switchport trunk allowed vlan 10,20,30,40,50,60,90
    switchport mode trunk
```

</div>
<div class="card">

### Configuration L3

```text
interface GigabitEthernet1/0/1
    no switchport // active le port L3
    ip address 172.31.253.2 255.255.255.252


ip routing
ip route 0.0.0.0 0.0.0.0 172.31.253.1
```

</div>
</div>

---

# Configuration de RT_ADMIN
## Interface vers RT_MEDIA (fibre optique)

```text
interface GigabitEthernet0/0/0
 media-type sfp // utilise le port SFP (fibre) au lieu du cuivre
 ip address 172.31.254.1 255.255.255.252
 duplex auto // duplex négocié automatiquement
 speed auto // vitesse négociée automatiquement
```

```text
interface GigabitEthernet0/2/0
 switchport access vlan 10
 switchport trunk native vlan 10
 switchport trunk allowed vlan 1,10,20,30,40,50,60,90
 switchport mode access
```

<div class="note"><strong>Pourquoi des VLAN :</strong> le routeur dispose de peu de ports. Il est possible d’y ajouter des ports L2 issus de modules de switch, d’où le fonctionnement par VLAN.</div>

---

# Routage statique de RT_ADMIN
## Routeur central : il doit connaître tous les chemins

```text
ip route 172.31.11.0 255.255.255.0 172.31.253.2
ip route 172.31.14.0 255.255.255.0 172.31.253.2
ip route 172.31.12.0 255.255.255.0 172.31.253.2
ip route 172.31.13.0 255.255.255.0 172.31.252.2
ip route 172.31.15.0 255.255.255.0 172.31.254.2
ip route 172.31.20.0 255.255.255.0 172.31.254.2
ip route 172.31.19.0 255.255.255.0 172.31.254.2
ip route 0.0.0.0 0.0.0.0 172.31.99.2          // lié au firewall : sortie par défaut vers l’ASA
ip route 172.31.0.0 255.255.0.0 203.0.113.2   // lié au firewall : route des réseaux internes vers l’ASA
```

<div class="note"><strong>Rôle central :</strong> RT_ADMIN est au milieu de la topologie ; il doit connaître les chemins vers tous les réseaux (SW_L3_ADMIN, RT_TECH, RT_MEDIA et le firewall).</div>

---

# Sous-interfaces sur RT_MEDIA
## Deux VLAN sur un seul port : Router-on-a-Stick

```text
interface GigabitEthernet0/0/1.60
 encapsulation dot1Q 60
 ip address 172.31.15.1 255.255.255.0
 ip helper-address 172.31.1.3
```

| Commande | Explication |
|---|---|
| `GigabitEthernet0/0/1.60` | Sous-interface logique du port physique ; le `.60` rappelle le VLAN 60 |
| `encapsulation dot1Q 60` | Les trames portant le tag 802.1Q **VLAN 60** sont traitées par cette sous-interface |
| `ip address 172.31.15.1 …` | Passerelle du VLAN 60 (Médiathèque interne) |
| `ip helper-address 172.31.1.3` | Relais des requêtes DHCP vers `SRV_DHCP` |

<div class="note"><strong>Pourquoi :</strong> à la médiathèque, 2 VLAN (60 et 90) passent par 1 seul port physique ; chaque VLAN a donc sa sous-interface.</div>

---

# Isolation des visiteurs : sous-interface VLAN 99
## Utilisateurs externes : un VLAN dédié et filtré

```text
interface GigabitEthernet0/0/2.99
 encapsulation dot1Q 99 native
 ip address 172.31.19.1 255.255.255.0
 ip helper-address 172.31.1.3
 ip access-group ACL_VLAN99_RESTRICT in
```

| Commande | Explication |
|---|---|
| `interface GigabitEthernet0/0/2.99` | Sous-interface dédiée au Wi-Fi visiteurs : le trafic externe est séparé du reste du réseau |
| `encapsulation dot1Q 99 native` | Associe la sous-interface au VLAN 99 ; `native` = trames reçues sans tag |
| `ip address 172.31.19.1 255.255.255.0` | Passerelle du réseau visiteurs `172.31.19.0/24` |
| `ip helper-address 172.31.1.3` | Relais des requêtes DHCP vers `SRV_DHCP` |
| `ip access-group ACL_VLAN99_RESTRICT in` | Applique l’ACL au trafic **entrant** des visiteurs, avant tout routage |

<div class="note"><strong>Pourquoi :</strong> ce sont des utilisateurs externes, il faut les isoler. La sous-interface crée la frontière, l’ACL contrôle ce qui la traverse.</div>

---

# Isolation des visiteurs : ACL

<style scoped>
pre { font-size: 12px; }
table { font-size: 13px; }
th, td { padding: 3px 8px; }
</style>

```text
ip access-list extended ACL_VLAN99_RESTRICT
 permit udp 172.31.19.0 0.0.0.255 host 172.31.1.3 eq bootps
 permit ip 172.31.19.0 0.0.0.255 172.31.1.0 0.0.0.15
 deny ip any any
```

| Commande | Explication |
|---|---|
| `ip access-list extended ACL_VLAN99_RESTRICT` | Crée l’ACL étendue (source, destination, protocole) |
| `permit udp … host 172.31.1.3 eq bootps` | Autorise les requêtes DHCP (UDP 67) vers `SRV_DHCP` |
| `permit ip … 172.31.1.0 0.0.0.15` | Autorise les visiteurs vers le réseau serveurs `172.31.1.0/28` |
| `deny ip any any` | Bloque tout le reste |


---

# Pare-feu Cisco ASA : zones et routage
## `outside` et `inside` : deux zones de confiance différentes

<style scoped>
pre { font-size: 11px; line-height: 1.2; }
table { font-size: 12px; }
th, td { padding: 2px 8px; }
</style>

<div class="grid">

```text
interface GigabitEthernet1/1
 nameif outside
 security-level 0
 ip address 203.0.113.2 255.255.255.252
!
interface GigabitEthernet1/2
 nameif inside
 security-level 100
 ip address 172.31.99.2 255.255.255.252

object network NET_MEDIA
 subnet 172.31.15.0 255.255.255.0
 nat (inside,outside) dynamic interface
!
route outside 0.0.0.0 0.0.0.0 203.0.113.1 1
route inside 172.31.0.0 255.255.0.0 172.31.99.1 1
```

| Élément | Rôle |
|---|---|
| `outside`, niveau 0 | Côté Internet : zone non fiable, trafic entrant bloqué par défaut |
| `inside`, niveau 100 | Côté réseau communal : zone de confiance, peut sortir vers `outside` |
| `object network NET_MEDIA` + `nat` | PAT : le réseau médiathèque `172.31.15.0/24` sort avec l’IP de l’interface `outside` |
| `route outside 0.0.0.0 …` | Sortie par défaut vers l’opérateur `203.0.113.1` |
| `route inside 172.31.0.0/16 …` | Retour vers les réseaux internes via `RT_ADMIN` (`172.31.99.1`) |

</div>

---

# Pare-feu Cisco ASA : ACL et inspection

```text
access-list OUTSIDE_IN extended permit icmp any any echo-reply
access-list OUTSIDE_IN extended permit icmp any any unreachable
access-list OUTSIDE_IN extended permit icmp any any

policy-map global_policy
 class inspection_default
```

| Commande | Explication |
|---|---|
| `access-list OUTSIDE_IN … echo-reply` | Autorise les réponses de ping venant de `outside` |
| `access-list OUTSIDE_IN … unreachable` | Autorise les messages « destination inaccessible » (diagnostic) |
| `access-list OUTSIDE_IN … permit icmp any any` | Autorise tout l’ICMP depuis l’extérieur |
| `policy-map global_policy` | Politique globale appliquée au trafic traversant l’ASA |
| `class inspection_default` | Inspection d’état des protocoles standard : le trafic retour des connexions initiées à l’intérieur est accepté |

<div class="note"><strong>ACL OUTSIDE_IN :</strong> comme <code>outside</code> a le niveau 0, tout ce qui arrive de l’extérieur est refusé sauf ce que l’ACL autorise ; ici uniquement l’ICMP, pour valider les tests de connectivité.</div>

---

# Simulation d’Internet : RT_INTERNET
## Une loopback pour représenter un serveur public

```text
interface Loopback1
 ip address 8.8.8.8 255.255.255.255
!
interface GigabitEthernet0/0/0
 ip address 203.0.113.1 255.255.255.252
 duplex auto
 speed auto

ip route 172.31.0.0 255.255.0.0 203.0.113.2
```

| Commande | Explication |
|---|---|
| `interface Loopback1` | Interface virtuelle, toujours active : elle simule un serveur sur Internet |
| `ip address 8.8.8.8 255.255.255.255` | Adresse publique (type DNS Google) en `/32` : cible de test pour la sortie Internet |
| `GigabitEthernet0/0/0` — `203.0.113.1/30` | Lien vers l’ASA (`203.0.113.2`) : côté opérateur |
| `duplex auto` / `speed auto` | Négociation automatique du lien |
| `ip route 172.31.0.0 255.255.0.0 203.0.113.2` | Route de retour vers tous les réseaux de la commune, via l’ASA |

<div class="note"><strong>Objectif :</strong> pas d’Internet réel dans Packet Tracer ; la loopback <code>8.8.8.8</code> permet de tester un ping vers l’extérieur et de valider NAT et pare-feu.</div>

---

# Conclusion personnelle
## Ce que m’apporte ce projet

<div class="grid3">
<div class="card">

### Bases de l’infrastructure
Ce projet développe très bien la compréhension de base d’une infrastructure réseau de petite entreprise.

</div>
<div class="card">

### Écosystème Cisco
Nous apprenons à travailler avec un produit propriétaire, Cisco, ce qui ouvre la possibilité de passer la certification **CCNA**.

</div>
<div class="card">

### Amélioration possible
Remplacer le routage statique par **OSPF** : les routes seraient apprises automatiquement, sans modifier chaque routeur à la main lors d’une extension.

</div>
</div>

---

<!-- _class: title -->

# Merci de votre attention
## Avez-vous des questions ?

