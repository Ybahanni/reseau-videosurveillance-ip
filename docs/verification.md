# Vérification et tests

Dans Packet Tracer, simuler les caméras par des PC (IP statiques 192.168.10.11 à .22, passerelle 192.168.10.1) et le NVR par un serveur (192.168.20.10, services HTTP/HTTPS actifs). Ajouter une capture de chaque test dans `captures/`.

## 1. Commutation

| Commande | Résultat attendu |
|---|---|
| `show vlan brief` (CORE, ACC1) | VLAN et ports conformes au plan |
| `show interfaces trunk` | G0/1 en trunk, VLAN autorisés 10,99, natif 999 |
| `show port-security` (ACC1) | Fa0/1 à Fa0/12 protégés, 1 MAC maximum |

- [ ] Test validé

## 2. Flux autorisés

| Test | Résultat attendu |
|---|---|
| Ping CAM-01 → NVR 192.168.20.10 | ✅ Succès |
| Ping NVR → CAM-05 | ✅ Succès (le NVR récupère les flux) |
| Navigateur PE-01 → http://192.168.20.10 | ✅ Page du NVR |
| Ping PE-01 → NVR | ✅ Succès |

- [ ] Test validé

## 3. Flux bloqués

| Test | Résultat attendu |
|---|---|
| Ping CAM-01 → PE-01 | ❌ Bloqué par ACL_CAMERAS |
| Ping CAM-01 → CAM-02 | ✅ Succès : même VLAN, pas de routage (voir note) |
| Ping PE-01 → CAM-01 | ❌ Bloqué par ACL_EXPLOITATION |
| `show access-lists` (CORE) | Compteurs sur les lignes deny |

- [ ] Test validé

> **Note :** l'ACL ne filtre que le trafic routé. Deux caméras du même VLAN peuvent encore communiquer entre elles. Sur du matériel réel, on bloque ce trafic avec `switchport protected` (PVLAN edge) sur les ports caméras. C'est une piste d'amélioration à tester.

## 4. Sécurité des ports

| Test | Résultat attendu |
|---|---|
| Remplacer CAM-03 par un autre PC sur Fa0/3 | Violation port security, trafic rejeté |
| `show port-security interface fa0/3` | Compteur de violations incrémenté |
| Brancher un switch sur Fa0/4 | Port en err-disabled (BPDU guard) |

- [ ] Test validé

## 5. NTP et administration

| Test | Résultat attendu |
|---|---|
| `show ntp status` (CORE) | Horloge synchronisée, stratum 3 |
| SSH depuis PC-ADMIN (192.168.99.10) vers ACC1 192.168.99.2 | ✅ Connexion |
| SSH depuis PE-01 vers le CORE | ❌ Refusé |

- [ ] Test validé
