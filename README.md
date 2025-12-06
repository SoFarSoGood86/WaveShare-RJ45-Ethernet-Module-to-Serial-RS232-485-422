# Guide d’installation et de tests pour Waveshare Serial POE + VEICHI AC70

Ce guide décrit comment installer, configurer et tester l’intégration Home Assistant pour contrôler un variateur VEICHI AC70 via un boîtier Waveshare RS232/485/422 TO POE ETH (B).

---

## 1️⃣ Prérequis

* Home Assistant 2023.11 ou supérieur
* Boîtier Waveshare RS232/485/422 TO POE ETH (B) connecté au réseau
* Variateur VEICHI AC70 avec Modbus activé
* GitHub CLI installé (pour release ou mise à jour)
* Python 3.10+ si utilisation du script externe

---

## 2️⃣ Installation via HACS

1. Ouvrir Home Assistant → HACS → Integrations → Custom repositories
2. Ajouter l’URL du dépôt :
   `https://github.com/SoFarSoGood86/waveshare_serial_poe`
3. Choisir **Category: Integration**
4. Installer l’intégration et redémarrer HA

---

## 3️⃣ Configuration dans HA (UI)

1. Configuration → Intégrations → Ajouter une intégration → **Waveshare Serial POE**
2. Renseigner les champs :

   * **IP du boîtier Waveshare** : ex. `192.168.1.50`
   * **Port TCP** : `23` (ou autre selon configuration)
   * **Mode physique** : RS-232 / RS-485 / RS-422
   * **Slave Modbus** : `1`
3. Valider et sauvegarder

### Options Flow

* Modifier les registres Modbus si nécessaire (par défaut VEICHI AC70)
* `reg_run`, `reg_frequency_set`, `reg_frequency_read`, `reg_current`, `reg_voltage`, `reg_fault`

---

## 4️⃣ Exemple YAML (optionnel)

```yaml
waveshare_serial_poe:
  host: 192.168.1.50
  port: 23
  mode: rs485
  slave_id: 1
  reg_run: 0x2000
  reg_frequency_set: 0x2001
  reg_frequency_read: 0x2103
  reg_current: 0x2100
  reg_voltage: 0x2101
  reg_fault: 0x2102
```

> À utiliser uniquement si vous préférez configurer via YAML au lieu de l’UI.

---

## 5️⃣ Vérification des entités

* `switch.waveshare_run` : ON/OFF pour démarrer/arrêter le moteur
* `number.waveshare_frequency` : régler la fréquence de consigne (Hz)
* `sensor.waveshare_frequency_actual` : fréquence réelle (Hz)
* `sensor.waveshare_current` : courant moteur (A)
* `sensor.waveshare_voltage` : tension DC (V)
* `sensor.waveshare_fault` : codes défauts

> Utiliser l’interface HA ou Developer Tools → States pour vérifier que les entités remontent correctement.

---

## 6️⃣ Test direct via script Python

### Étapes

1. Placer le script `tools/test_ac70.py` sur un PC connecté au réseau du Waveshare
2. Modifier l’adresse IP et le port si nécessaire
3. Exécuter :

```bash
python3 tools/test_ac70.py
```

4. Vérifier :

   * Lecture de la fréquence actuelle
   * Démarrage du moteur (RUN)

---

## 7️⃣ Tests unitaires (PyTest)

```bash
pytest tests/
```

* Vérifie la communication mockée et le setup HA

---

## 8️⃣ Conseils pratiques

* Toujours vérifier le mode RS-485/RS-232/RS-422 du boîtier avant d’envoyer des commandes
* Utiliser le service `waveshare_serial_poe.send_raw` pour tester des trames Modbus personnalisées
* Surveillez les logs HA pour les erreurs de communication ou timeout
* Pour tout changement de registre VEICHI, mettre à jour Options Flow ou YAML

---

## 9️⃣ Mise à jour et release

* Mettre à jour le dépôt via GitHub → HACS → Update
* Pour créer une nouvelle release : utiliser le script `release.sh` avec la nouvelle version

```bash
./release.sh v1.1.0
```

* Le ZIP sera automatiquement attaché à la release GitHub

---

Guide prêt pour utilisation et tests de l’intégration Waveshare + VEICHI AC70 dans Home Assistant.
