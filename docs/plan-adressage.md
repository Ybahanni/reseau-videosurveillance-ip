# Plan d'adressage

## VLAN

| VLAN | Nom | Réseau | Passerelle (SVI CORE) |
|---|---|---|---|
| 10 | CAMERAS | 192.168.10.0/24 | 192.168.10.1 |
| 20 | NVR_SERVEURS | 192.168.20.0/24 | 192.168.20.1 |
| 30 | EXPLOITATION | 192.168.30.0/24 | 192.168.30.1 |
| 99 | MANAGEMENT | 192.168.99.0/24 | 192.168.99.1 |
| 999 | PARKING | – | – |

Adressage **statique**, comme c'est l'usage en vidéosurveillance : chaque caméra garde une adresse fixe, reportée sur le plan d'implantation et dans le NVR.

## Équipements

| Équipement | VLAN | Adresse | Port |
|---|---|---|---|
| CAM-01 à CAM-12 | 10 | 192.168.10.11 à .22 | ACC1 Fa0/1 – Fa0/12 |
| NVR-01 | 20 | 192.168.20.10 | CORE Fa0/1 |
| PE-01 (poste d'exploitation) | 30 | 192.168.30.11 | CORE Fa0/10 |
| PE-02 (poste d'exploitation) | 30 | 192.168.30.12 | CORE Fa0/11 |
| PC-ADMIN (poste d'administration) | 99 | 192.168.99.10 | CORE Fa0/24 |
| CORE | 99 | 192.168.99.1 | – |
| ACC1 | 99 | 192.168.99.2 | – |

## Liens

| De | Vers | Rôle |
|---|---|---|
| ACC1 G0/1 | CORE G0/1 | Trunk 802.1Q (VLAN 10, 99) |

## Flux autorisés

| Source | Destination | Service | Autorisé |
|---|---|---|---|
| Caméras | NVR | Tous (RTSP, ONVIF, SDK) | ✅ |
| Caméras | CORE 192.168.10.1 | NTP (UDP 123) | ✅ |
| Caméras | Tout le reste | – | ❌ |
| Postes d'exploitation | NVR | HTTP 80, HTTPS 443, RTSP 554, SDK 8000, ICMP | ✅ |
| Postes d'exploitation | Caméras | – | ❌ |
| NVR | Caméras | Tous (récupération des flux) | ✅ |
