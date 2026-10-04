# Rapport Technique : Déploiement et Sécurisation de l'Infrastructure Réseau Multi-Sites

**Auteur :** Petro Maltsev  
**Date :** 05.10.2026  
**Outil de simulation :** Cisco Packet Tracer

---

## 1. Introduction et Objectifs du Projet

L'objectif de ce projet consiste à concevoir, déployer et sécuriser l'infrastructure réseau d'une administration communale répartie sur trois bâtiments distincts :
1. **Centre Administratif** (Serveurs, Administration, Direction, Guichets, Urbanisme) ;
2. **Services Techniques** (Postes techniques) ;
3. **Médiathèque** (Postes fixes, postes publics multimédia et accès Wi-Fi invités).

Le réseau doit garantir la segmentation logique par VLAN, l'attribution automatique des adresses via un serveur DHCP centralisé, le routage inter-VLAN (L3 et Router-on-a-Stick), l'isolation sécurisée des réseaux non fiables par listes de contrôle d'accès (ACL) et la sortie vers Internet filtrée via un pare-feu dédié (Cisco ASA).

---

## 2. Architecture Réseau et Plan d'Adressage

### 2.1 Segmentation des VLANs
* **VLAN 10 (VLAN_ADMIN) :** Infrastructure réseau, serveurs centraux (DHCP, Web/SRV), commutateurs et routeurs.
* **VLAN 20 (VLAN_GUICHETS) :** Guichets d'accueil et service social.
* **VLAN 30 (VLAN_URB_AM) :** Service Urbanisme et Aménagement du territoire.
* **VLAN 40 (VLAN_FIN_DIR) :** Direction générale et service des Finances.
* **VLAN 50 (VLAN_TECH) :** Bâtiment des Services Techniques.
* **VLAN 60 (VLAN_FIXE) :** Postes de travail internes de la Médiathèque.
* **VLAN 90 (VLAN_PUBLIC) :** Postes multimédias en libre accès pour les usagers.
* **VLAN 99 (VLAN_VISITE) :** Réseau sans-fil (Wi-Fi) dédié aux visiteurs extérieurs.
* **VLAN 999 (Transit ASA) :** Sous-réseau d'interconnexion isolé entre le routeur central et le pare-feu.

### 2.2 Plan d'Adressage IP (Synthèse)

| Sous-réseau | Rôle / VLAN | Passerelle par défaut (Gateway) | Masque |
| :--- | :--- | :--- | :--- |
| `172.31.1.0/28` | VLAN 10 (Serveurs) | `172.31.1.1` (`RT_ADMIN`) | `255.255.255.240` |
| `172.31.11.0/24` | VLAN 20 (Guichets) | `172.31.11.1` (`SW_L3_ADMIN`) | `255.255.255.0` |
| `172.31.12.0/24` | VLAN 30 (Urbanisme) | `172.31.12.1` (`SW_L3_ADMIN`) | `255.255.255.0` |
| `172.31.14.0/24` | VLAN 40 (Finance/Dir) | `172.31.14.1` (`SW_L3_ADMIN`) | `255.255.255.0` |
| `172.31.13.0/24` | VLAN 50 (Technique) | `172.31.13.1` (`RT_TECH`) | `255.255.255.0` |
| `172.31.15.0/24` | VLAN 60 (Média Fixe) | `172.31.15.1` (`RT_MEDIA`) | `255.255.255.0` |
| `172.31.20.0/24` | VLAN 90 (Média Public) | `172.31.20.1` (`RT_MEDIA`) | `255.255.255.0` |
| `172.31.19.0/24` | VLAN 99 (Wi-Fi Visite) | `172.31.19.1` (`RT_MEDIA`) | `255.255.255.0` |
| `172.31.99.0/30` | VLAN 999 (Liaison ASA) | `172.31.99.1` (`RT_ADMIN`) / `172.31.99.2` (`ASA`) | `255.255.255.252` |
| `203.0.113.0/30` | WAN Internet | `203.0.113.2` (`ASA`) / `203.0.113.1` (`ISP`) | `255.255.255.252` |

---

## 3. Travaux Réalisés et Implémentation

### 3.1 Commutation et Relais DHCP (Centre Administratif)
* Déploiement du commutateur multi-couches **`SW_L3_ADMIN`** (Cisco Catalyst 3650) pour le routage inter-VLAN local des services administratifs via des interfaces virtuelles de commutation (**SVI** `interface Vlan 20, 30, 40`).
* Activation globale du routage avec la directive `ip routing`.
* Configuration de la redirection des requêtes de découverte DHCP vers le serveur centralisé `SRV_DHCP` (`172.31.1.3` / `172.31.1.10`) à l'aide de l'instruction `ip helper-address` sur chaque SVI d'accès.
* Harmonisation des liaisons agrégées (Trunks 802.1Q) vers les commutateurs d'étage (`SW_GUICHETS`, `SW_URB_AM`, `SW_FIN_DIR`) avec déclaration uniforme du Native VLAN.

### 3.2 Interconnexions WAN et Support Fibre Optique
* Établissement des liaisons inter-routeurs en sous-réseaux point-à-point `/30`.
* Interconnexion du site distant **`RT_MEDIA`** vers le routeur central **`RT_ADMIN`** au moyen d'une liaison à fibre optique sur les interfaces combinées SFP (`GigabitEthernet0/0/0`).

### 3.3 Déploiement et Sécurisation du Réseau Invité (Wi-Fi)
* Raccordement du point d'accès `AP_VISITE_1` pour diffuser la connectivité sans-fil aux smartphones des usagers.
* Mise en place d'un filtrage strict par liste de contrôle d'accès (**ACL étendue**) appliquée en entrée sur la passerelle du réseau visiteurs :
  * Autorisation du trafic DHCP vers le serveur ;
  * Autorisation de l'accès aux ressources partagées indispensables (VLAN Serveurs) ;
  * Blocage explicite et systématique de tout accès vers les réseaux internes des employés (`deny ip any any`).

### 3.4 Intégration du Pare-feu de Bordure (Cisco ASA 5506-X) et Accès Internet
* Raccordement physique et logique du pare-feu sur le routeur central `RT_ADMIN`.
* Définition des zones de sécurité :
  * **Inside** (Niveau de sécurité 100) : connecté au cœur de réseau via `172.31.99.2`.
  * **Outside** (Niveau de sécurité 0) : connecté au routeur fournisseur d'accès `RT_INTERNET` via `203.0.113.2`.
* Routage bidirectionnel :
  * Route par défaut (`0.0.0.0/0`) pointant vers la passerelle opérateur (`203.0.113.1`).
  * Route statique de retour vers le super-réseau `172.31.0.0/16` relayée par `RT_ADMIN`.
* Configuration du mécanisme de filtrage dynamique et de l'inspection ICMP (`inspect icmp`) au sein de la politique globale afin de valider la connectivité sortante des postes de travail.

---

## 4. Difficultés Rencontrées, Diagnostics et Solutions

### Problème 1 : Conflits ARP et blocage du routage inter-VLAN
* **Symptôme :** Les postes clients n'arrivaient pas à joindre leur passerelle ou recevaient des paquets en boucle.
* **Cause :** Des interfaces de gestion SVI configurées par erreur avec les adresses IP des passerelles sur les commutateurs de niveau 2 (L2) interceptaient les requêtes ARP destinées au commutateur L3.
* **Solution :** Suppression des interfaces VLAN parasites sur les commutateurs d'accès pour ne conserver que la passerelle active sur `SW_L3_ADMIN`.

### Problème 2 : Liaison fibre optique inactive entre RT_MEDIA et RT_ADMIN
* **Symptôme :** Malgré le raccordement physique, l'interface `GigabitEthernet0/0/0` restait à l'état `Link Down` et refusait les paramètres duplex/speed conventionnels.
* **Cause :** Sur les routeurs Cisco ISR 4331, les interfaces combinées nécessitent une sélection matérielle explicite de la terminaison optique SFP.
* **Solution :** Configuration explicite du type de support optique dans l'interface :

```text
interface GigabitEthernet0/0/0
 media-type sfp
 no shutdown
```

### Problème 3 : Échec du retour de ping (ICMP Type 3) depuis le réseau Wi-Fi
* **Symptôme :** Le smartphone recevait bien son adresse IP mais les pings vers les serveurs échouaient. L'analyse sous Packet Tracer (Simulation) indiquait un paquet rouge au niveau de RT_ADMIN (ICMP Host Unreachable).
* **Cause :** RT_MEDIA acheminait bien les requêtes, mais RT_ADMIN ne possédait pas de route inverse pour réexpédier les réponses vers le réseau 172.31.19.0/24.
* **Solution :** Ajout de la route statique de retour sur RT_ADMIN vers RT_MEDIA :

```text
ip route 172.31.19.0 255.255.255.0 172.31.254.2
```

### Problème 4 : Absence d'interfaces de routage L3 libres sur RT_ADMIN
* **Symptôme :** Impossibilité d'assigner une adresse IP sur les ports d'extension disponibles (GigabitEthernet0/2/1), ces derniers appartenant à un module de commutation L2 intégré (NIM/ESW) qui rejette la commande no switchport.
* **Cause :** Limitation du matériel émulé ne disposant plus de modules purement L3.
* **Solution :** Création d'une interface virtuelle de transit (SVI VLAN 999) associée au port L2 configuré en mode accès :

```text
interface Vlan 999
 ip address 172.31.99.1 255.255.255.252
 no shutdown
exit
interface GigabitEthernet0/2/1
 switchport mode access
 switchport access vlan 999
 no shutdown
```

---

## 5. Procédures de Validation et Tests Effectués

**Attribution automatique d'adresses (DHCP) :**
Validation sur l'ensemble des postes clients de chaque VLAN (PC_GUICHETS, PC_URB_AM, PC_MEDIA_FIXE, SP_VISITEUR) de la bonne réception d'une adresse IP, du masque, du DNS et de la passerelle correspondante.

**Isolation sécurisée du VLAN 99 :**
Pings concluants vers SRV_DHCP (172.31.1.3).
Pings rejetés vers les postes administratifs (172.31.11.x, 172.31.15.x), confirmant l'efficacité de l'ACL ACL_VLAN99_RESTRICT.

**Sortie WAN / Internet :**
Validation de bout en bout des requêtes ICMP depuis les postes du réseau interne (PC_MEDIA_FIXE_2) vers la passerelle distante RT_INTERNET (203.0.113.1).

---

## 6. Conclusion

L'ensemble des objectifs fixés dans le cahier des charges a été atteint :
- La topologie réseau est stable et hiérarchisée ;
- Le cloisonnement des données administratives est effectif ;
- La sécurité périmétrique et l'accès extérieur sont assurés de manière centralisée par le pare-feu Cisco ASA.