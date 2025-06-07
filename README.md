# BeeData

### Système embarqué de récupération de données pour ruche connectée

Le projet **BeeData** a pour objectif de concevoir un système embarqué capable de collecter et stocker des données environnementales à l’intérieur d’une ruche, tout en garantissant une autonomie énergétique maximale.

---

## Matériel utilisé

Pour mener à bien ce projet, les composants suivants seront utilisés :

* [**ESP32**](Eps32.md) : microcontrôleur principal, chargé de la gestion du système et de la communication.
* [**Un capteur de température et d'humidité SHT31**](SHT31.md) : pour mesurer les conditions environnementales internes à la ruche.
* [**Un micro INMP441**](Micro_INMP441.md) : microphone numérique pour la détection sonore des activités des abeilles.
* [**Module SPI**](Module_SPI.md) : pour l’enregistrement des données sur une carte SD.
* [**Panneau solaire 10W 5V**](Panneau_Solaire.md) : source d’énergie principale du système.
* [**Batterie lithium-ion Li-Po 3.7V 5000mAh**](Li-Po.md) : pour stocker l’énergie et assurer l’alimentation continue du système.
* [**MCP73871**](MCP73871.md) : contrôleur de charge assurant la gestion de la recharge de la batterie via le panneau solaire.
* [**Module abaisseur de tension MT3608**](MT3608.md) : pour adapter la tension de la batterie à celle requise par l’ESP32.

---

## Fonctionnement

Les données mesurées par les capteurs seront dans un premier temps stockées sur une carte SD grâce au module SPI. Une communication à distance en temps réel pourra ensuite être mise en place (par exemple via Wi-Fi ou LoRa, selon les contraintes du terrain).

L'ensemble du système sera alimenté par une solution solaire autonome : un panneau solaire 10W 5V rechargera une batterie lithium-ion Li-Po 3.7V 5000mAh, elle-même gérée par le MCP73871. La tension sera ensuite régulée pour alimenter l’ESP32 grâce au module MT3608.

Deux types de mesures pourront être effectués : soit une prise de son seule durant 24 heures, soit une prise de mesure d'humidité et de température avec trois sondes SHT31 réparties comme sur le schéma ci-dessous, ainsi qu'une prise de son de 10 minutes, le tout enregistré sur la carte SD.

![image](https://github.com/user-attachments/assets/0bb17719-9a3e-4fd7-8da9-8ad5d7beec80)

---

## Analyse de la consommation et de l'autonomie

### Rendements considérés

* Module abaisseur MT3608 : **85 %** d’efficacité
* Contrôleur MCP73871 : **80 %** d’efficacité
* Pertes diverses (câblage, veille, conversion) estimées à : **5 %**

**Rendement global estimé = 0,85 × 0,80 × 0,95 ≈ 0,65 (65 %)**

---

### Scénario 1 : Micro actif en continu pendant 24h

| Composant      | Consommation estimée (mA) |
| -------------- | ------------------------- |
| ESP32          | 160                       |
| Micro INMP441  | 1.4                       |
| Carte SD (SPI) | 80                        |
| **Total**      | **241.4 mA**              |

**Consommation ajustée (avec pertes)** :
`241.4 / 0.65 ≈ 371.4 mA`

**Autonomie sur batterie seule (5000 mAh)** :
`5000 / 371.4 ≈ 13.5 heures`

---

### Scénario 2 : Mesures horaires (3 SHT31 + 10 min audio)

Chaque heure :

* 3 mesures température/humidité (\~1 min)
* 10 minutes d’enregistrement audio
* 47 minutes de veille

| Activité               | Estimation (mAh/h) |
| ---------------------- | ------------------ |
| ESP32 (actif 13 min/h) | 34.7               |
| Micro INMP441 (10 min) | 0.23               |
| Capteurs SHT31 ×3      | 0.025              |
| Carte SD (13 min)      | 17.3               |
| **Total**              | **52.3 mAh/h**     |

**Consommation ajustée (avec pertes)** :
`52.3 / 0.65 ≈ 80.5 mAh/h`

**Autonomie sur batterie seule (5000 mAh)** :
`5000 / 80.5 ≈ 62.1 heures` (\~2,6 jours)

---

### Apport solaire estimé

* Ensoleillement moyen : **5 h/jour**
* Courant utile moyen fourni : **\~1 A**
* Puissance nominale : 5 V × 1 A × 5 h = 25 Wh ≈ 6750 mAh (à 3.7 V)

**Apport solaire ajusté (avec pertes)** :
`6750 × 0.65 ≈ 4388 mAh / jour`

---

### Synthèse

| Scénario                         | Consommation journalière ajustée | Production solaire ajustée | Autonomie théorique         |
| -------------------------------- | -------------------------------- | -------------------------- | --------------------------- |
| Micro actif 24h                  | \~8914 mAh                       | \~4388 mAh                 | \~**13.5 h** (pas autonome) |
| Température + audio chaque heure | \~1932 mAh                       | \~4388 mAh                 | **Autosuffisant**           |

---

## Conclusion

* Le système **n'est pas autonome** si le micro est actif 24h/24 sans interruption.
* Le scénario de mesures horaires avec audio de 10 minutes reste **autonome** grâce à la recharge solaire.
* Pour améliorer l’autonomie en continu, il faudra envisager :

  * une optimisation logicielle (modes veille profonds, diminution de la fréquence audio)
  * une batterie de plus grande capacité ou un panneau solaire plus puissant.
