²# BeeData

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
* [**Batteries lithium-ion Li-Po 3.7V 5000mAh**](Li-Po.md) : pour stocker l’énergie et assurer l’alimentation continue du système.
* [**MCP73871**](MCP73871.md) : contrôleur de charge assurant la gestion de la recharge de la batterie via le panneau solaire.
* [**Module abaisseur de tension MT3608**](MT3608.md) : pour adapter la tension de la batterie à celle requise par l’ESP32.

---

## Fonctionnement

Les données mesurées par les capteurs seront dans un premier temps stockées sur une carte SD grâce au module SPI. Une communication à distance en temps réel pourra ensuite être mise en place (par exemple via Wi-Fi ou LoRa, selon les contraintes du terrain).

L'ensemble du système sera alimenté par une solution solaire autonome : un panneau solaire 10W 5V rechargera une batterie lithium-ion Li-Po 3.7V 5000mAh, elle-même gérée par le MCP73871. La tension sera ensuite régulée pour alimenter l’ESP32 grâce au module MT3608.

