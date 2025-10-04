# 🎯 Tiroir Motorisé Intelligent — Tutoriel Complet
## Projet ESP32 + TMC2209 + Home Assistant

> 📅 **Démarré :** Octobre 2025  
> ✅ **Statut :** Phase 2 en cours — Système motorisé opérationnel  
> 🎓 **Niveau :** Intermédiaire (électronique + domotique)

---

## 📖 Table des matières

1. [Présentation du projet](#-présentation-du-projet)
2. [Ce que tu vas construire](#-ce-que-tu-vas-construire)
3. [Matériel nécessaire](#-matériel-nécessaire)
4. [Guide de construction](#-guide-de-construction-étape-par-étape)
5. [Configuration logicielle](#-configuration-logicielle)
6. [Utilisation quotidienne](#-utilisation-quotidienne)
7. [Automatisations avancées](#-automatisations-avancées)
8. [Dépannage](#-dépannage)

---

## 🎬 Présentation du projet

### **Le problème**
Tu travailles sur un bureau et tu veux :
- Ranger ton clavier/souris quand tu ne travailles pas (bureau propre)
- Les déployer rapidement quand tu t'assois
- **Sans** avoir à les manipuler manuellement à chaque fois

### **La solution**
Un **tiroir motorisé silencieux** qui :
- S'ouvre/ferme en **10 secondes** (vitesse réglable)
- Est **100% silencieux** (technologie TMC2209 StealthChop)
- Se pilote via **Home Assistant** (smartphone, automatisations, vocal)
- A des **boutons physiques LED** pour contrôle manuel
- Détecte les **blocages** et s'arrête automatiquement (sécurité)

---

## 🎨 Ce que tu vas construire

### **Vue d'ensemble du système**

```
┌─────────────────────────────────────────────────┐
│            BUREAU (vue de dessous)              │
│                                                 │
│  ┌─────────────────────────────────────────┐   │
│  │  [Moteur NEMA17] ──→ Courroie GT2      │   │
│  │         ↓                    ↓          │   │
│  │    [Poulie 20T]          [Poulie]      │   │
│  │                                         │   │
│  │  ═══════════════════════════════════   │   │
│  │  │  TIROIR (rails télescopiques)  │   │   │
│  │  │  [Clavier] [Souris]            │   │   │
│  │  ═══════════════════════════════════   │   │
│  └─────────────────────────────────────────┘   │
│                                                 │
│  [Boîtier électronique]  [3 Boutons LED]       │
└─────────────────────────────────────────────────┘
```

### **Composants électroniques**

```
         Home Assistant
              ↕ WiFi
         ┌─────────┐
         │  ESP32  │ ← Cerveau du système
         └────┬────┘
              │
    ┌─────────┼─────────┐
    │         │         │
┌───▼──┐  ┌──▼───┐  ┌──▼──────┐
│ TMC  │  │INA219│  │ Boutons │
│ 2209 │  │ I²C  │  │   LED   │
└───┬──┘  └──────┘  └─────────┘
    │
┌───▼──────┐
│ NEMA 17  │ ← Moteur silencieux
└──────────┘
```

---

## 🧰 Matériel nécessaire

### **🔧 Composants électroniques**

| Pièce | Quantité | Prix unitaire | Lien |
|-------|----------|---------------|------|
| **ESP32 DevKit V4** | 1 | ~7€ | [AliExpress](https://fr.aliexpress.com/item/1005006212080137.html) |
| **Moteur NEMA 17** | 1 | ~10€ | [AliExpress](https://fr.aliexpress.com/item/32665922113.html) |
| **Driver TMC2209 V2** | 1 | ~8€ | [AliExpress](https://fr.aliexpress.com/item/1005004063785803.html) |
| **Module INA219** | 1 | ~2€ | [AliExpress](https://fr.aliexpress.com/item/1005007533576335.html) |
| **Alimentation 24V 3A** | 1 | ~12€ | [AliExpress](https://fr.aliexpress.com/item/1005007087317638.html) |
| **Buck Converter LM2596** | 1 | ~2€ | [AliExpress](https://fr.aliexpress.com/item/1005006149348817.html) |
| **Boutons LED 12mm (x3)** | 1 lot | ~5€ | [AliExpress](https://fr.aliexpress.com/item/1005003575736338.html) |
| **Résistances 220Ω** | 3 | ~1€ | [AliExpress](https://fr.aliexpress.com/item/1005008902058496.html) |
| **Capteurs TCRT5000** | 2 | ~3€ | [AliExpress](https://fr.aliexpress.com/item/1899471977.html) |

**Total électronique :** ~50€

### **🔩 Composants mécaniques**

| Pièce | Quantité | Prix unitaire | Lien |
|-------|----------|---------------|------|
| **Kit courroie GT2 + poulies 20T** | 1 | ~8€ | [AliExpress](https://fr.aliexpress.com/item/32303569726.html) |
| **Rails télescopiques 400mm** | 1 paire | ~15€ | [AliExpress](https://fr.aliexpress.com/item/1005002992085234.html) |
| **Câbles Dupont** | 1 lot | ~3€ | Recherche "Lot 120 câbles Dupont" |
| **Visserie M3/M4** | 1 kit | ~5€ | Recherche "Kit visserie makers" |

**Total mécanique :** ~30€

### **🛠️ Outils nécessaires**

- ✅ Multimètre digital (vérifier tensions)
- ✅ Tournevis cruciforme (réglage potentiomètres)
- ✅ Pince coupante + dénude-fil
- ✅ Fer à souder (optionnel mais recommandé)
- ✅ Ordinateur avec port USB (flashage ESP32)

---

## 🏗️ Guide de construction (étape par étape)

### **📅 Phase 1 : Câblage électronique (2-3h)**

#### **Étape 1.1 : Préparation**
```
✓ Pose tous les composants sur une table propre
✓ Identifie chaque composant avec son nom
✓ Prépare un espace de travail éclairé
✓ Lis le document Branchement.md en entier
```

#### **Étape 1.2 : Réglages préliminaires CRITIQUES**

> ⚠️ **CES RÉGLAGES SONT OBLIGATOIRES** — Ne pas les faire = risque de destruction

**A. Réglage Buck Converter (24V → 5V)**
```
1. Branche UNIQUEMENT alim 24V sur Buck Converter
2. Mesure tension sortie avec multimètre
3. Tourne potentiomètre pour obtenir exactement 5.0V
4. Débranche TOUT
```
➡️ [Voir procédure détaillée](./Branchement.md#2️⃣-buck-converter-lm2596-24v--5v)

**B. Réglage TMC2209 VREF**
```
1. Branche alim 24V sur TMC2209 (SANS moteur)
2. Mesure tension entre potentiomètre et GND
3. Ajuste à 0.55V (pour 1A RMS)
4. Débranche TOUT
```
➡️ [Voir procédure détaillée](./Branchement.md#4️⃣-tmc2209-driver-moteur-silencieux)

#### **Étape 1.3 : Câblage alimentation**

Suis l'ordre **EXACT** :

1. **Rail GND commun** (critique pour éviter les problèmes)
   - Utilise un bornier à vis ou une breadboard
   - Connecte TOUS les GND ensemble
   
2. **Alimentation 24V → INA219 → TMC2209**
   - INA219 en série sur ligne +24V
   
3. **Buck Converter → ESP32**
   - OUT+ vers VIN ESP32
   - OUT- vers GND commun

➡️ [Voir schéma complet](./Branchement.md#1️⃣-alimentation-24v--distribution)

#### **Étape 1.4 : Câblage signaux de contrôle**

Connecte dans cet ordre :

1. **ESP32 → TMC2209** (GPIO 14, 12, 13)
2. **ESP32 → INA219** (I²C : GPIO 21, 22)
3. **ESP32 → Boutons LED** (GPIO 25, 26, 27, 32, 33, 35)

➡️ [Voir tableau de brochage complet](./Branchement.md#3️⃣-esp32-devkit-38-pins)

#### **Étape 1.5 : Tests de sécurité**

**AVANT de brancher l'alimentation :**
```
□ Vérifier continuité GND (toutes les masses bipent)
□ Pas de court-circuit +24V / GND (résistance > 10kΩ)
□ Buck réglé à 5.0V
□ TMC VREF = 0.55V
□ Polarité alim correcte (rouge = +, noir = -)
```

➡️ [Checklist complète](./Branchement.md#-checklist-avant-premier-démarrage)

---

### **📅 Phase 2 : Configuration logicielle (1-2h)**

#### **Étape 2.1 : Installation ESPHome**

Si tu n'as pas déjà Home Assistant :

**Option A : ESPHome Dashboard (recommandé)**
```bash
# Sur un Raspberry Pi ou PC Linux
pip3 install esphome
esphome dashboard config/
```

**Option B : Via Home Assistant Add-on**
```
1. Ouvre Home Assistant
2. Settings → Add-ons → Add-on Store
3. Cherche "ESPHome" → Install
4. Start → Open Web UI
```

#### **Étape 2.2 : Création du device**

1. Dans ESPHome Dashboard, clique **"New Device"**
2. Nom : `tiroir-clavier`
3. Type : `ESP32`
4. Connecte l'ESP32 en USB
5. Clique **"Install"** → Sélectionne le port USB

#### **Étape 2.3 : Configuration YAML**

Copie le code du fichier `esp-proto-tiroir.yaml` fourni dans l'artifact.

**Personnalise ces lignes :**
```yaml
substitutions:
  name: "tiroir-clavier"
  static_ip: 192.168.1.xxx  # Ton IP locale

wifi:
  ssid: "TonSSID"
  password: "TonMotDePasse"

api:
  encryption:
    key: "génère-une-clé-aléatoire"  # ESPHome génère ça automatiquement
```

#### **Étape 2.4 : Premier flash**

```
1. Sauvegarde le fichier YAML
2. Clique "Install" → "Plug into this computer"
3. Attends la compilation (~5 min)
4. Flash automatique via USB
5. Débranche USB, branche alimentation 24V
6. L'ESP32 devrait apparaître dans Home Assistant
```

---

### **📅 Phase 3 : Tests fonctionnels (30 min)**

#### **Test 1 : Connexion WiFi**
```
✓ ESP32 doit apparaître dans ESPHome Dashboard (online)
✓ Ping 192.168.1.xxx doit répondre
✓ Logs ESPHome doivent s'afficher
```

#### **Test 2 : Boutons physiques**
```
1. Appuie sur bouton VERT → LED doit s'allumer 1s
2. Appuie sur bouton BLEU → LED doit s'allumer 1s
3. Appuie sur bouton ROUGE → LED doit s'allumer 1s
4. Vérifie dans les logs : "Bouton Ouvrir: ON"
```

#### **Test 3 : Moteur (À VIDE)**
```
⚠️ IMPORTANT : Moteur NON connecté au tiroir
1. Dans Home Assistant → Cover "Tiroir Clavier"
2. Clique "Ouvrir" → Moteur doit tourner silencieusement
3. Clique "Stop" → Arrêt immédiat
4. Clique "Fermer" → Rotation inverse
```

#### **Test 4 : Protection surintensité**
```
1. Bloque manuellement le moteur pendant rotation
2. Vérifie arrêt automatique après 1-2 secondes
3. Logs doivent afficher "Surintensité détectée: X.XX A"
```

---

### **📅 Phase 4 : Installation mécanique (2-3h)**

#### **Étape 4.1 : Montage rails télescopiques**
```
1. Fixe la partie fixe sous le bureau (vis M4)
2. Fixe la partie mobile sous le plateau tiroir
3. Teste la glisse manuellement (doit être fluide)
```

#### **Étape 4.2 : Installation courroie + poulies**
```
1. Fixe poulie motrice sur axe NEMA 17 (vis de serrage)
2. Positionne poulie folle à l'autre extrémité
3. Tends la courroie GT2 (doit "claquer" comme une corde de guitare)
4. Fixe courroie sur le plateau tiroir (collage + vis)
```

#### **Étape 4.3 : Positionnement capteurs fins de course**
```
1. Colle cibles blanches (carton 2×2cm) sur le tiroir
   - Position fermée : début de course
   - Position ouverte : fin de course
2. Fixe capteurs TCRT5000 en face des cibles
3. Ajuste distance capteur/cible à 5-8mm
4. Règle potentiomètres (LED s'allume quand cible proche)
```

#### **Étape 4.4 : Calibration finale**
```
1. Ferme complètement le tiroir manuellement
2. Dans Home Assistant, clique "Reset Position"
3. Ouvre via bouton → Note le nombre de pas dans les logs
4. Mets à jour steps_per_100percent dans le code
5. Reflash l'ESP32
6. Teste ouverture/fermeture complète
```

---

## 💻 Configuration logicielle

### **Entités disponibles dans Home Assistant**

Une fois flashé, tu verras ces entités :

| Type | Nom | Fonction |
|------|-----|----------|
| 🪟 **Cover** | `cover.tiroir_clavier` | Contrôle position 0-100% |
| 🟢 **Switch** | `switch.led_verte` | Allume/éteint LED verte |
| 🔵 **Switch** | `switch.led_bleue` | Allume/éteint LED bleue |
| 🔴 **Switch** | `switch.led_rouge` | Allume/éteint LED rouge |
| 💡 **Switch** | `switch.feedback_led_boutons` | Active/désactive feedback tactile |
| ⚡ **Sensor** | `sensor.tiroir_ina219_courant` | Courant moteur en temps réel |
| 📊 **Sensor** | `sensor.tiroir_ina219_puissance` | Puissance consommée |
| 🔋 **Sensor** | `sensor.tiroir_ina219_tension` | Tension bus 24V |

### **Contrôle via l'interface**

```yaml
# Carte Lovelace simple
type: entities
title: Tiroir Clavier
entities:
  - entity: cover.tiroir_clavier
    name: Position
  - type: divider
  - entity: switch.led_verte
    name: 🟢 LED Verte
  - entity: switch.led_bleue
    name: 🔵 LED Bleue
  - entity: switch.led_rouge
    name: 🔴 LED Rouge
  - type: divider
  - entity: switch.feedback_led_boutons
    name: 💡 Feedback tactile
  - type: divider
  - entity: sensor.tiroir_ina219_courant
    name: Courant moteur
```

---

## 🎮 Utilisation quotidienne

### **Contrôle manuel (boutons physiques)**

| Bouton | Action | LED |
|--------|--------|-----|
| 🟢 **VERT** | Déployer le tiroir | Clignote 1s |
| 🔵 **BLEU** | Rentrer le tiroir | Clignote 1s |
| 🔴 **ROUGE** | Arrêt d'urgence | Clignote 1s |

### **Contrôle via Home Assistant**

**Smartphone / Tablette**
```
1. Ouvre l'app Home Assistant
2. Cherche "Tiroir Clavier"
3. Slide pour ajuster position (0-100%)
4. Ou tape sur OPEN/CLOSE/STOP
```

**Contrôle vocal (si configuré)**
```
"Hey Google, ouvre le tiroir clavier"
"Alexa, ferme le tiroir du bureau"
"Siri, arrête le tiroir"
```

---

## 🤖 Automatisations avancées

### **Auto 1 : Ouverture automatique (capteur de présence)**

```yaml
automation:
  - alias: "Déployer tiroir si je m'assois"
    trigger:
      - platform: state
        entity_id: binary_sensor.chaise_occupee
        to: 'on'
        for: '00:00:05'  # Délai 5s pour éviter faux positifs
    condition:
      - condition: state
        entity_id: cover.tiroir_clavier
        state: 'closed'
      - condition: time
        after: '08:00:00'
        before: '20:00:00'  # Seulement en journée
    action:
      - service: cover.open_cover
        entity_id: cover.tiroir_clavier
```

### **Auto 2 : Fermeture nocturne (20h)**

```yaml
automation:
  - alias: "Ranger tiroir le soir"
    trigger:
      - platform: time
        at: "20:00:00"
    condition:
      - condition: state
        entity_id: cover.tiroir_clavier
        state: 'open'
    action:
      - service: cover.close_cover
        entity_id: cover.tiroir_clavier
      - service: notify.mobile_app
        data:
          message: "🌙 Tiroir rangé pour la nuit"
```

### **Auto 3 : Mode nuit LED (désactivation automatique)**

```yaml
automation:
  - alias: "Éteindre LED boutons la nuit"
    trigger:
      - platform: time
        at: "22:00:00"
    action:
      - service: switch.turn_off
        entity_id:
          - switch.led_verte
          - switch.led_bleue
          - switch.led_rouge
          - switch.feedback_led_boutons
  
  - alias: "Rallumer LED boutons le matin"
    trigger:
      - platform: time
        at: "08:00:00"
    action:
      - service: switch.turn_on
        entity_id: switch.feedback_led_boutons
```

### **Auto 4 : Alerte surintensité (blocage détecté)**

```yaml
automation:
  - alias: "Alerte blocage tiroir"
    trigger:
      - platform: numeric_state
        entity_id: sensor.tiroir_ina219_courant
        above: 1.5
        for: '00:00:02'
    action:
      - service: cover.stop_cover
        entity_id: cover.tiroir_clavier
      - service: switch.turn_on
        entity_id: switch.led_rouge
      - service: notify.mobile_app
        data:
          message: "⚠️ Tiroir bloqué ! Courant anormal : {{ states('sensor.tiroir_ina219_courant') }}A"
          title: "Alerte Tiroir"
```

---

## 🔧 Dépannage

### **Problème 1 : ESP32 ne se connecte pas au WiFi**

**Symptômes :** LED bleue clignote rapidement, pas d'apparition dans Home Assistant

**Solutions :**
```
✓ Vérifie SSID/password dans le code (sensible à la casse)
✓ Assure-toi que le WiFi est en 2.4GHz (ESP32 ne supporte pas 5GHz)
✓ Rapproche l'ESP32 du routeur lors du premier démarrage
✓ Regarde les logs via USB : esphome logs tiroir-clavier.yaml
```

### **Problème 2 : Moteur ne tourne pas**

**Symptômes :** Commande envoyée mais pas de mouvement

**Solutions :**
```
✓ Vérifie GPIO 13 (EN) → doit être LOW pour activer
✓ Vérifie connexion VIO (3.3V) sur TMC2209
✓ Mesure VREF (doit être 0.55V)
✓ Teste avec "Test Rotation Horaire" dans ESPHome
✓ Vérifie que les 4 fils moteur sont bien connectés
```

### **Problème 3 : Mouvement saccadé / bruyant**

**Symptômes :** Moteur fait du bruit, vibrations

**Solutions :**
```
✓ Réduis vitesse : max_speed: 600 steps/s
✓ Augmente accélération douce : acceleration: 300 steps/s²
✓ Vérifie tension courroie (doit être bien tendue)
✓ Vérifie dissipateur thermique sur TMC2209
✓ Réduis VREF si surchauffe (essaie 0.50V)
```

### **Problème 4 : LED boutons ne s'allument pas**

**Symptômes :** Boutons fonctionnent mais pas de feedback visuel

**Solutions :**
```
✓ Vérifie polarité LED (broche 2 = +, broche 3 = -)
✓ Vérifie résistances 220Ω présentes
✓ Teste LED directement : 3.3V → R 220Ω → LED+ → LED- → GND
✓ Vérifie que switch.feedback_led_boutons = ON
```

### **Problème 5 : Arrêts intempestifs**

**Symptômes :** Tiroir s'arrête au milieu de la course

**Solutions :**
```
✓ Vérifie INA219 courant (doit être < 1.5A)
✓ Augmente seuil surintensité dans le code (essaie 2.0A)
✓ Vérifie alimentation 24V 3A (pas de chute de tension)
✓ Réduis charge sur le tiroir (poids clavier + souris)
```

---

## 📊 Monitoring & Maintenance

### **Surveillance recommandée**

Ajoute ces sensors dans Home Assistant :

```yaml
sensor:
  - platform: history_stats
    name: "Utilisations tiroir aujourd'hui"
    entity_id: cover.tiroir_clavier
    state: 'opening'
    type: count
    start: '{{ now().replace(hour=0, minute=0, second=0) }}'
    end: '{{ now() }}'

  - platform: statistics
    name: "Courant moteur moyen"
    entity_id: sensor.tiroir_ina219_courant
    state_characteristic: mean
    max_age:
      hours: 24
```

### **Maintenance périodique**

**Mensuelle :**
- [ ] Vérifier tension courroie
- [ ] Nettoyer capteurs TCRT5000 (poussière)
- [ ] Vérifier température TMC2209 (doit rester tiède)

**Trimestrielle :**
- [ ] Lubrifier rails télescopiques (graisse silicone)
- [ ] Vérifier serrages vis moteur
- [ ] Tester protection surintensité (bloquer manuellement)

**Annuelle :**
- [ ] Remplacer courroie GT2 si usure visible
- [ ] Nettoyer complètement le système
- [ ] Vérifier toutes les connexions électriques

---

## 📚 Ressources complémentaires

### **Documentation technique**
- 📄 [Branchement.md](./Branchement.md) — Schémas électriques détaillés
- 📄 [esp-proto-tiroir.yaml](./esp-proto-tiroir.yaml) — Code ESPHome complet
- 🔗 [Documentation ESPHome](https://esphome.io/)
- 🔗 [TMC2209 Datasheet](https://www.trinamic.com/products/integrated-circuits/details/tmc2209-la/)

### **Communauté & Support**
- 💬 Forum Home Assistant : [community.home-assistant.io](https://community.home-assistant.io/)
- 💬 Reddit : r/homeassistant
- 💬 Discord ESPHome : [discord.gg/KhAMKrd](https://discord.gg/KhAMKrd)

---

## 🎉 Félicitations !

Si tu es arrivé jusqu'ici, tu as maintenant un **tiroir motorisé intelligent** complètement fonctionnel ! 🚀

### **Améliorations possibles :**
- ✨ Ajout d'un capteur de poids (détecter présence clavier)
- ✨ LED RGB sous le tiroir (effet lumineux)
- ✨ Intégration Matter/Thread (futur-proof)
- ✨ Deuxième tiroir synchronisé (gauche + droite)

**Partage ton projet !** N'hésite pas à poster des photos de ta réalisation. 📸

---

> 📅 **Dernière mise à jour :** Octobre 2025  
> ✍️ **Version :** 2.0  
> 📧 **Questions ?** Consulte la section [Dépannage](#-dépannage)
