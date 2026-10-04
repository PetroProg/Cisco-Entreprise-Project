# Journal de travail - Projet P_RES-129

**Intitulé du projet :** Conception et simulation d’un réseau local communal  
**Établissement :** ETML (Ecole Technique des Métiers de Lausanne)  
**Période de réalisation :** 24 périodes (18 heures)  
**Groupe 6 :** Maltsev Petro  

---

## Suivi des heures et avancement

| Date | Durée (minutes) | Tâches réalisées / Activités | Remarques, difficultés rencontrées & solutions |
| :--- | :---: | :--- | :--- |
| 24.08.2026 | 45 min | Installation de la machine virtuelle dans VMware et la configuration en installant Cisco | Snapshot "Config terminé" est fait. |
| 25.08.2026 | 90 min | Conception du schéma réseau sur Visio de 3 bâtiments. | |
| 25.08.2026 | 90 min | La choix et le nommage des VLANs. Les adresses IP assignées aux VLANs. Quelques corrections apportées au schéma du réseau. | |
| 31.08.2026 | 45 min | La création du plan d'adressage. | |
| 01.09.2026 | 90 min | Création du schéma physique sur Cisco Packet Tracer | |
| 01.09.2026 | 90 min | Configuration des serveurs, des adresses IP et autre. | |
| 07.09.2026 | 70 min | Configuration du vlan 20 (VLAN_GUICHETS). En plus le DHCP pour ce sous-réseau est configuré. | |
| 08.09.2026 | 90 min | Configuration du vlan 40 (VLAN_FIN_DIR). En plus le DHCP pour ce sous-réseau est configuré. | |
| 08.09.2026 | 45 min | Configuration du vlan 30 (VLAN_URB_AM). En plus le DHCP pour ce sous-réseau est configuré. Dépannage du routage inter-VLAN sur le commutateur L3 (SW_L3_ADMIN) et harmonisation des trunks vers les switchs d'accès. | Le trafic entre VLANs était bloqué : activation du routage IP (`ip routing`), suppression des SVI parasites sur SW_URB_AM et réinitialisation des directives Native VLAN sur les trunks. |
| 08.09.2026 | 45 min | Configuration du vlan 50 (VLAN_TECH). Le routage entre 2 routeurs. Rétablissement de la liaison fibre optique entre les routeurs RT_MEDIA et RT_ADMIN. | Le port `GigabitEthernet0/0/0` restait down : activation explicite de l'émetteur-récepteur avec la commande `media-type sfp` et mise en service (`no shutdown`). |
| 22.09.2026 | 45 min | Récréation du schéma sur draw.io | |
| 22.09.2026 | 90 min | Configuration du vlan 50. Configuration du bâtiment MEDIA (VLAN 60 et VLAN 90) et résolution du problème de routage inter-VLAN. | Conflit ARP : des adresses IP de passerelle étaient assignées sur les SVI des switchs L2 (SW_MEDIA). Suppression des interfaces VLAN sur les commutateurs d'accès pour laisser le routeur gérer le routage. |
| 28.09.2026 | 45 min | Schéma fini dans draw.io. Déploiement du réseau Wi-Fi invité (VLAN 99, 172.31.19.0/24) avec point d'accès AP_VISITE_1, smartphone et sécurisation par ACL (`ACL_VLAN99_RESTRICT`). | Problème d'encapsulation avec le point d'accès non-taggé : configuration directe de l'IP sur le port du routeur avec helper-address. Ajout d'une route vers RT_ADMIN et autorisation stricte vers les serveurs uniquement. |
| 28.09.2026 | 45 min | Plan d'adressage terminé. Intégration du pare-feu Cisco ASA 5506-X sur RT_ADMIN via VLAN de transit 999, routage WAN et configuration de la sortie Internet. | Absence de port L3 libre sur RT_ADMIN : passage par une SVI VLAN 999 sur le module switch. Configuration des zones inside/outside, routage vers RT_INTERNET (203.0.113.1) et validation des flux ICMP. |

