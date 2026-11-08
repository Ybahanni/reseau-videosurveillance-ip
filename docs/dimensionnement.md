# Dimensionnement

Hypothèses : 12 caméras 4 MP, compression H.265, 25 i/s, enregistrement continu, conservation de 30 jours.

> 30 jours est la durée de conservation maximale généralement retenue en France pour la vidéoprotection (Code de la sécurité intérieure). À adapter selon le cahier des charges et l'autorisation préfectorale.

## 1. Débit réseau

| Paramètre | Valeur |
|---|---|
| Débit par caméra (flux principal) | 4 Mbit/s |
| Débit total caméras → NVR | 12 × 4 = **48 Mbit/s** |
| Uplink ACC1 → CORE | 1 Gbit/s, soit moins de 5 % d'utilisation |
| Flux secondaires (visualisation) | environ 1 Mbit/s par caméra affichée, négligeable |

Conclusion : un uplink Gigabit unique suffit. L'EtherChannel est prévu pour la redondance, pas pour la capacité.

## 2. Stockage

Stockage = débit × durée

48 Mbit/s × 86 400 s × 30 jours / 8 = **15,55 To**

| | Capacité |
|---|---|
| Besoin brut | 15,6 To |
| Avec 20 % de marge (pics de débit, scènes chargées) | **18,7 To** |
| Proposition | NVR 4 baies, 4 × 8 To en RAID 5, soit 24 To utiles |

## 3. Budget PoE

| Hypothèse | Puissance par caméra | Total 12 caméras |
|---|---|---|
| Consommation réelle typique (caméra fixe sans IR fort) | environ 7 W | **84 W** |
| Pire cas, réservation classe 3 (802.3af) | 15,4 W | **184,8 W** |

Choix : switch d'accès PoE+ avec un budget d'au moins **370 W** (format courant sur un 24 ports), ce qui couvre le pire cas avec de la marge pour des caméras PTZ ou IR ajoutées plus tard.

## 4. QoS (matériel réel)

Les caméras marquent leurs flux en DSCP (souvent AF41 ou CS4, réglable dans l'interface de la caméra). Sur le matériel réel :

```
mls qos
interface range Fa0/1 - 12
 mls qos trust dscp
interface GigabitEthernet0/1
 mls qos trust dscp
```

Sur un réseau dédié et peu chargé (moins de 5 %), la QoS sert surtout de garde-fou si le réseau devient un jour mutualisé avec la bureautique.
