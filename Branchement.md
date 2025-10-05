# 🔌 README — Branchements Tiroir Motorisé ESP32

> 📅 Dernière mise à jour : Octobre 2025  
> ✅ Statut : Phase 2 — Boutons câblés et fonctionnels

---

## 🎯 Vue d'ensemble du système

Ce document décrit le **câblage électrique complet** du tiroir motorisé piloté par ESP32 + TMC2209.

### **Architecture générale**
```
Alim 24V 3A
    ├──→ INA219 (mesure courant) → TMC2209 (moteur)
    ├──→ Buck Converter LM2596 → ESP32 (5V)
    └──→ Rail GND commun (toutes les masses)
         ├──→ 3 Boutons LED
         └──→ 2 Capteurs TCRT5000 (à venir)
```

---

## 📦 Matériel utilisé

| Composant | Quantité | Fonction | Statut |
|-----------|----------|----------|--------|
| **Alim 24V 3A** | 1 | Alimentation principale | ✅ Opérationnel |
| **Buck Converter LM2596** | 1 | 24V → 5V pour ESP32 | ✅ Réglé à 5.0V |
| **ESP32 DevKit 38 pins** | 1 | Contrôleur principal | ✅ Flashé |
| **TMC2209 V2.0** | 1 | Driver moteur silencieux | ✅ VREF 0.55V |
| **INA219** | 1 | Mesure courant/tension I2C | ✅ Monitoring actif |
| **Moteur NEMA 17** | 1 | Moteur pas-à-pas 42N.cm | ✅ Silencieux |
| **Boutons LED 12mm** | 3 | Contrôle manuel (vert/bleu/rouge) | ✅ Câblés |
| **Résistances 220Ω** | 3 | Protection LED boutons | ✅ En place |
| **Capteurs TCRT5000** | 2 | Fins de course (à câbler) | ⏳ En attente modules |

---

## 🔌 Schéma de câblage principal

### **1️⃣ Alimentation 24V → Distribution**

```
┌─────────────────────┐
│   ALIMENTATION      │
│      24V 3A         │
└────┬───────────┬────┘
     │ +24V      │ GND (-)
     │           │
     │           └────────────────┐
     │                            │
     ├──→ Buck Converter IN+      │
     │                            │
     └──→ INA219 VIN+             │
                                  │
                            [GND COMMUN]
                            Tous les GND
                            du système
```

---

### **2️⃣ Buck Converter LM2596 (24V → 5V)**

| Borne Buck | Connexion | Fonction |
|------------|-----------|----------|
| **IN+** | ← Alim 24V `(+)` | Entrée 24V |
| **IN-** | → [GND COMMUN] | Masse entrée |
| **OUT+** | → ESP32 `VIN` | Sortie 5V régulée |
| **OUT-** | → [GND COMMUN] | Masse sortie |

> ⚙️ **RÉGLAGE OBLIGATOIRE** : Ajuster le potentiomètre à **5.0V** (±0.1V) avec un multimètre AVANT de brancher l'ESP32 !

**Procédure de réglage :**
```
1. Brancher UNIQUEMENT alim 24V + Buck Converter
2. Mesurer tension entre OUT+ et OUT- avec multimètre
3. Tourner potentiomètre (tournevis cruciforme)
4. Ajuster à exactement 5.0V (±0.1V)
5. Débrancher AVANT de connecter l'ESP32
```

> ⚠️ **DANGER** : Une tension > 5.5V **détruirait** l'ESP32 instantanément !

---

### **3️⃣ ESP32 DevKit 38 pins**

#### **Alimentation**
| Pin ESP32 | Connexion | Fonction |
|-----------|-----------|----------|
| **VIN** | ← Buck Converter `OUT+` | Alimentation 5V |
| **GND** | → [GND COMMUN] | Masse |
| **3.3V** | → TMC2209 `VIO` + INA219 `VCC` | Sortie logique 3.3V |

#### **Contrôle moteur TMC2209**
| Pin ESP32 | Pin TMC2209 | Fonction |
|-----------|-------------|----------|
| **GPIO 12** | `STEP` | Signal de pas |
| **GPIO 14** | `DIR` | Direction (avant/arrière) |
| **GPIO 13** | `EN` | Enable moteur (LOW=actif) |

#### **Communication I2C (INA219)**
| Pin ESP32 | Pin INA219 | Fonction |
|-----------|------------|----------|
| **GPIO 21** | `SDA` | I2C Data |
| **GPIO 22** | `SCL` | I2C Clock |

#### **Boutons de contrôle physique** ✅ **CÂBLÉS**
| Pin ESP32 | Fonction | Type | Détails |
|-----------|----------|------|---------|
| **GPIO 25** | LED Bouton VERT | OUTPUT | Via résistance 220Ω |
| **GPIO 26** | Bouton VERT (Ouvrir) | INPUT_PULLUP | Broche 1 bouton |
| **GPIO 27** | LED Bouton BLEU | OUTPUT | Via résistance 220Ω |
| **GPIO 32** | Bouton BLEU (Fermer) | INPUT_PULLUP | Broche 1 bouton |
| **GPIO 33** | LED Bouton ROUGE | OUTPUT | Via résistance 220Ω |
| **GPIO 35** | Bouton ROUGE (Stop) | INPUT_ONLY | Broche 1 bouton |

#### **Capteurs fins de course (TCRT5000)** ⏳ **À CÂBLER**
| Pin ESP32 | Capteur | Position |
|-----------|---------|----------|
| **GPIO 18** | TCRT5000 #1 | Fin de course FERMÉ |
| **GPIO 19** | TCRT5000 #2 | Fin de course OUVERT |

---

### **4️⃣ TMC2209 (Driver moteur silencieux)**

#### **Alimentation puissance**
| Borne TMC2209 | Connexion | Fonction |
|---------------|-----------|----------|
| **VM** | ← INA219 `VIN-` | Alimentation moteur 24V |
| **GND (power)** | → [GND COMMUN] | Masse puissance (bornier vis) |

#### **Alimentation logique**
| Pin TMC2209 | Connexion | Fonction |
|-------------|-----------|----------|
| **VIO** | ← ESP32 `3.3V` | Alimentation logique |
| **GND (logic)** | → [GND COMMUN] | Masse logique (pin header) |

#### **Signaux de contrôle**
| Pin TMC2209 | Connexion | Fonction |
|-------------|-----------|----------|
| **STEP** | ← ESP32 `GPIO 12` | Signal de pas |
| **DIR** | ← ESP32 `GPIO 14` | Direction |
| **EN** | ← ESP32 `GPIO 13` | Enable (LOW=actif) |

#### **Connexion moteur**
| Borne TMC2209 | Fil moteur | Bobine |
|---------------|------------|--------|
| **1A** | Fil 1 | Bobine A |
| **1B** | Fil 2 | Bobine A |
| **2A** | Fil 3 | Bobine B |
| **2B** | Fil 4 | Bobine B |

> 💡 **Note** : Si le moteur tourne à l'envers, inverse une paire (ex: swap 1A ↔ 1B)

> ⚙️ **RÉGLAGE VREF** : Ajuster le potentiomètre à **0.55V** pour 1A RMS (mesure entre potentiomètre et GND avec moteur débranché)

**Formule** : `VREF = Courant_RMS × 0.5 × Rsense`
- Pour 1A RMS : `VREF = 1.0 × 0.5 × 0.11 = 0.55V`
- Pour 0.8A RMS : `VREF = 0.44V`

---

### **5️⃣ INA219 (Mesure courant I2C)**

#### **Mesure de puissance (High-Side)**
| Pin INA219 | Connexion | Fonction |
|------------|-----------|----------|
| **VIN+** | ← Alim 24V `(+)` | Entrée courant à mesurer |
| **VIN-** | → TMC2209 `VM` | Sortie vers charge (moteur) |

> 🔍 **Principe** : L'INA219 est **en série** sur la ligne 24V pour mesurer le courant consommé par le moteur

#### **Communication I2C**
| Pin INA219 | Connexion | Fonction |
|------------|-----------|----------|
| **VCC** | ← ESP32 `3.3V` | Alimentation logique |
| **GND** | → [GND COMMUN] | Masse |
| **SDA** | ← ESP32 `GPIO 21` | I2C Data |
| **SCL** | ← ESP32 `GPIO 22` | I2C Clock |

---

### **6️⃣ Moteur NEMA 17 (42N.cm)**

Le moteur NEMA 17 a **4 fils** (2 bobines) — **pas de fil GND**.

```
Moteur NEMA 17 → TMC2209
  Fil 1 (bobine A) → 1A
  Fil 2 (bobine A) → 1B
  Fil 3 (bobine B) → 2A
  Fil 4 (bobine B) → 2B
```

> ⚠️ **Attention** : Ne JAMAIS débrancher le moteur quand le système est sous tension → risque de destruction du TMC2209 !

---

### **7️⃣ Boutons LED 12mm (Contrôle physique)** ✅ **NOUVEAUTÉ**

Chaque bouton a **4 broches** avec brochage spécifique :

#### **Identification des broches (vue arrière)**
```
Vue arrière du bouton :
┌─────────────────────┐
│   Bouton 12mm       │
│                     │
│    Potentiomètre   │ ← Pas de potentiomètre sur ce modèle
│        (LED)       │
│                     │
└──┬────┬────┬────┬───┘
   1    2    3    4

Broche 1 : Interrupteur (contact NO)
Broche 2 : LED Anode (+)
Broche 3 : LED Cathode (-)
Broche 4 : Interrupteur (contact NO)
```

---

#### **🟢 BOUTON VERT (Ouvrir le tiroir)**

```
PARTIE INTERRUPTEUR :
┌─────────────────────────────────────┐
│ Broche 1 → GPIO 26 (INPUT_PULLUP)  │
│ Broche 4 → GND commun               │
└─────────────────────────────────────┘

PARTIE LED :
┌─────────────────────────────────────────────┐
│ Broche 2 (+ LED) → Résistance 220Ω → GPIO 25 │
│ Broche 3 (- LED) → GND commun               │
└─────────────────────────────────────────────┘
```

**Schéma détaillé :**
```
         3.3V (ESP32)
            │
      [INPUT_PULLUP]
            │
    GPIO 26 ◄───┬──── Broche 1 (bouton)
                │
         Broche 4 (bouton) ──→ GND commun


         GPIO 25 ──→ R 220Ω ──→ Broche 2 (LED +)
                                    │
                            Broche 3 (LED -) ──→ GND commun
```

---

#### **🔵 BOUTON BLEU (Fermer le tiroir)**

```
PARTIE INTERRUPTEUR :
┌─────────────────────────────────────┐
│ Broche 1 → GPIO 32 (INPUT_PULLUP)  │
│ Broche 4 → GND commun               │
└─────────────────────────────────────┘

PARTIE LED :
┌─────────────────────────────────────────────┐
│ Broche 2 (+ LED) → Résistance 220Ω → GPIO 27 │
│ Broche 3 (- LED) → GND commun               │
└─────────────────────────────────────────────┘
```

---

#### **🔴 BOUTON ROUGE (Arrêt d'urgence)**

```
PARTIE INTERRUPTEUR :
┌─────────────────────────────────────┐
│ Broche 1 → GPIO 35 (INPUT_ONLY)    │
│ Broche 4 → GND commun               │
└─────────────────────────────────────┘

PARTIE LED :
┌─────────────────────────────────────────────┐
│ Broche 2 (+ LED) → Résistance 220Ω → GPIO 33 │
│ Broche 3 (- LED) → GND commun               │
└─────────────────────────────────────────────┘
```

> ⚠️ **Particularité GPIO 35** : Ce pin est **INPUT_ONLY** sur ESP32, il ne peut PAS piloter de LED. C'est pourquoi la LED rouge est sur GPIO 33.

---

#### **📋 Tableau récapitulatif des connexions boutons**

| Bouton | Broche 1 (Switch) | Broche 2 (LED+) | Broche 3 (LED-) | Broche 4 (Switch) |
|--------|-------------------|-----------------|-----------------|-------------------|
| 🟢 **VERT** | GPIO 26 | R220Ω → GPIO 25 | GND | GND |
| 🔵 **BLEU** | GPIO 32 | R220Ω → GPIO 27 | GND | GND |
| 🔴 **ROUGE** | GPIO 35 | R220Ω → GPIO 33 | GND | GND |

---

#### **⚙️ Résistances 220Ω (OBLIGATOIRES)**

**Pourquoi ?**
- GPIO ESP32 sortent **3.3V**
- LED ont typiquement une chute de tension de **2.0-2.2V**
- Courant optimal LED : **10-15mA**

**Calcul :**
```
R = (Vsource - VLED) / ILED
R = (3.3V - 2.0V) / 0.015A
R = 1.3V / 0.015A
R ≈ 87Ω minimum

→ On utilise 220Ω pour limiter à ~6mA (sécurité + durée de vie LED)
```

**Montage physique :**
```
Option A : Soudure directe
─────────────────────────
GPIO 25 ──→ [R 220Ω] ──→ Fil court ──→ Broche 2 bouton

Option B : Breadboard (test)
─────────────────────────────
GPIO 25 → Breadboard → R 220Ω → Breadboard → Broche 2 bouton
```

---

### **8️⃣ Capteurs TCRT5000 (Fins de course)** ⏳ **À CÂBLER**

> 📦 **Modules avec PCB en attente de livraison**

#### **Brochage module TCRT5000 (3 broches)**
```
┌─────────────────────┐
│  Module TCRT5000    │
│                     │
│  [Potentiomètre]    │ ← Réglage sensibilité
│                     │
│  VCC  GND  OUT      │
│   │    │    │       │
└───┼────┼────┼───────┘
    │    │    │
    │    │    └─── Signal digital (HIGH = détecté)
    │    └──────── Masse
    └───────────── Alimentation 3.3V
```

#### **Câblage prévu (à implémenter)**

**Capteur position FERMÉE :**
```
VCC → 3.3V ESP32
GND → GND commun
OUT → GPIO 18
```

**Capteur position OUVERTE :**
```
VCC → 3.3V ESP32
GND → GND commun
OUT → GPIO 19
```

#### **Réglage potentiomètre (lors de l'installation)**
```
1. Alimente le capteur (3.3V + GND)
2. Place cible blanche à ~5mm du capteur
3. Tourne le potentiomètre jusqu'à ce que :
   - LED module s'allume quand cible proche
   - LED s'éteint quand cible éloignée
4. Teste avec ESPHome (logs doivent afficher détection)
```

---

## 🔥 Rail GND commun — Point critique

**TOUS les GND doivent être reliés ensemble** pour créer une référence électrique commune.

### **Liste des masses à connecter :**
```
[GND COMMUN] ← Point central
    ├─ Alim 24V (-)
    ├─ Buck Converter IN-
    ├─ Buck Converter OUT-
    ├─ TMC2209 GND (power) — bornier vis
    ├─ TMC2209 GND (logic) — pin header
    ├─ ESP32 GND
    ├─ INA219 GND
    ├─ Bouton VERT (broches 3 + 4)
    ├─ Bouton BLEU (broches 3 + 4)
    ├─ Bouton ROUGE (broches 3 + 4)
    └─ Capteurs TCRT5000 GND (à venir)
```

### **Méthode de distribution GND**

**Option A : Bornier à vis (recommandé)**
```
Utilise un bornier 8-12 positions :
- 1 connexion depuis Alim 24V (-)
- 1 connexion vers chaque composant
- Avantage : Connexions solides, faciles à débugger
```

**Option B : Breadboard (prototypage)**
```
Rail GND breadboard :
- Tous les GND y sont branchés
- 1 seul fil vers ESP32 GND
- Avantage : Rapide pour tester
- Inconvénient : Connexions fragiles
```

**Option C : Soudure (version finale)**
```
Hub GND soudé :
- Tous les fils GND torsadés ensemble
- Étamés et soudés
- Gaine thermorétractable
- Avantage : Connexion ultra-fiable
```

---

## ⚙️ Réglages AVANT mise sous tension

### **1. Réglage Buck Converter (OBLIGATOIRE)**

```bash
PROCÉDURE :
1. Brancher UNIQUEMENT alim 24V + Buck Converter
2. Mesurer tension entre OUT+ et OUT- avec multimètre
3. Tourner potentiomètre (tournevis cruciforme)
   - Sens horaire = augmente tension
   - Sens anti-horaire = diminue tension
4. Ajuster à exactement 5.0V (±0.1V)
5. Débrancher AVANT de connecter l'ESP32
```

> ⚠️ **DANGER** : Une tension > 5.5V **détruirait** l'ESP32 instantanément !  
> ✅ **Vérification** : Re-mesurer après 5 minutes (stabilisation thermique)

---

### **2. Réglage TMC2209 VREF (OBLIGATOIRE)**

```bash
PROCÉDURE :
1. Brancher alim 24V + TMC2209 (moteur DÉBRANCHÉ)
2. Mesurer tension entre potentiomètre et GND
   - Pointe rouge multimètre : sur vis potentiomètre
   - Pointe noire multimètre : sur GND logic ou power
3. Tourner potentiomètre (tournevis cruciforme)
4. Ajuster à 0.55V pour 1A RMS
5. Débrancher et connecter le moteur
```

**Formule** : `VREF = Courant_RMS × 0.5 × Rsense (0.11Ω)`
- Pour 1A RMS : `VREF = 1.0 × 0.5 × 0.11 = 0.55V`
- Pour 0.8A RMS : `VREF = 0.44V`
- Pour 1.2A RMS : `VREF = 0.66V`

> ⚠️ **Ne JAMAIS dépasser 1.0V** → risque de surchauffe moteur !  
> 🌡️ **Monitoring** : Touche le TMC2209 après 5 min de fonctionnement (doit rester tiède, pas brûlant)

---

## ✅ Checklist avant premier démarrage

### **Tests de sécurité (multimètre obligatoire)**

- [ ] **Continuité GND** : Toutes les masses bipent entre elles
  ```
  Test : Mode continuité, touche GND ESP32 + GND Buck → bip
  ```

- [ ] **Pas de court-circuit** : Résistance entre +24V et GND > 10kΩ
  ```
  Test : Mode ohmmètre, mesure entre +24V alim et GND commun
  Résultat OK : > 10kΩ (typiquement 50-100kΩ)
  ```

- [ ] **Buck réglé** : Tension sortie = 5.0V (±0.1V)
  ```
  Test : Alim 24V + Buck seuls, mesure OUT+/OUT-
  ```

- [ ] **TMC VREF** : Tension potentiomètre = 0.55V
  ```
  Test : Alim 24V + TMC seul (sans moteur), mesure potentiomètre/GND
  ```

- [ ] **Polarité alim** : Rouge = +24V, Noir = GND
  ```
  Vérification visuelle : Fils bien insérés dans bornier
  ```

### **Vérifications visuelles**

- [ ] **Dissipateur TMC2209** : Bien collé sur le chip (contact thermique)
- [ ] **Câbles serrés** : Tous les borniers à vis bien serrés (test en tirant légèrement)
- [ ] **Pas de fils dénudés** : Pas de risque de contact entre +24V et GND
- [ ] **Moteur connecté** : 4 fils dans le bon ordre (1A, 1B, 2A, 2B)
- [ ] **Résistances 220Ω** : Une par LED, bien connectées
- [ ] **Polarité boutons LED** : Broche 2 = +, Broche 3 = -

---

## 🚀 Procédure de mise sous tension

### **Étape 1 : Test alimentation (5 min)**
```
Configuration : Alim 24V + Buck Converter uniquement
Action :
1. Brancher alim 24V
2. Vérifier LED Buck Converter (rouge allumée)
3. Mesurer 5V entre Buck OUT+ et OUT-
4. Débrancher si OK
Résultat attendu : 5.0V stable (±0.1V)
```

### **Étape 2 : Test ESP32 seul (10 min)**
```
Configuration : + ESP32 (sans moteur, sans boutons)
Action :
1. Connecter ESP32 VIN au Buck OUT+
2. Connecter ESP32 GND au GND commun
3. Brancher alim 24V
4. Vérifier LED ESP32 (bleue allumée)
5. Connecter USB et ouvrir terminal série
Résultat attendu : ESP32 boot, WiFi connecté
```

### **Étape 3 : Test boutons LED (15 min)**
```
Configuration : + 3 boutons LED câblés
Action :
1. Flash le code ESPHome complet
2. Appuie sur chaque bouton un par un
3. Vérifie LED s'allume 1s puis s'éteint
4. Vérifie logs ESPHome : "Bouton XXX: ON"
Résultat attendu : Feedback LED fonctionnel
```

### **Étape 4 : Test moteur à vide (20 min)**
```
Configuration : + Moteur NEMA17 (non attaché au tiroir)
Action :
1. Connecter moteur au TMC2209
2. Dans Home Assistant → "Tiroir Clavier"
3. Clique "Ouvrir" → Observer rotation silencieuse
4. Clique "Stop" → Arrêt immédiat
5. Vérifie INA219 : Courant ~0.5-1.0A
Résultat attendu : Rotation fluide, silencieuse, arrêt net
```

### **Étape 5 : Test avec charge (30 min)**
```
Configuration : Système complet monté sur tiroir
Action :
1. Fixe moteur sous bureau
2. Installe courroie + poulies
3. Teste ouverture/fermeture lente
4. Ajuste vitesse si nécessaire
5. Vérifie INA219 : Courant <1.5A
Résultat attendu : Mouvement fluide du tiroir réel
```

---

## 🔧 Dépannage rapide

| Problème | Cause probable | Solution | Vérification |
|----------|----------------|----------|--------------|
| **ESP32 ne s'allume pas** | Buck mal réglé ou GND absent | Vérifier 5V + continuité GND | Mesure tension OUT+/OUT- |
| **Moteur ne tourne pas** | TMC2209 EN mal câblé | Vérifier GPIO 13 → LOW | Logs : "Enable moteur ON" |
| **Moteur chauffe** | VREF trop élevé | Réduire à 0.5V | Toucher TMC après 5 min |
| **INA219 affiche 0A** | VIN+/VIN- mal câblés | Vérifier série sur ligne 24V | Mesure tension VIN+ = 24V |
| **TMC2209 chauffe** | Pas de dissipateur | Coller dissipateur thermique | Température <60°C |
| **Mouvement saccadé** | Alimentation insuffisante | Vérifier alim 24V 3A | Mesure tension sous charge |
| **LED bouton ne s'allume pas** | Polarité inversée | Swap broches 2 et 3 | Test LED direct avec 3.3V |
| **Bouton ne répond pas** | Mauvais câblage switch | Vérifier broches 1 et 4 | Test continuité à l'appui |

---

## 📚 Documentation technique

### **Datasheets**
- [TMC2209 Datasheet](https://www.trinamic.com/products/integrated-circuits/details/tmc2209-la/)
- [INA219 Datasheet](https://www.ti.com/lit/ds/symlink/ina219.pdf)
- [LM2596 Datasheet](https://www.ti.com/lit/ds/symlink/lm2596.pdf)
- [ESP32 Pinout](https://randomnerdtutorials.com/esp32-pinout-reference-gpios/)

### **Outils recommandés**
- Multimètre digital (mesure tension/continuité)
- Tournevis cruciforme petit (réglage potentiomètres)
- Pince coupante + dénude-fil
- Fer à souder 30W (optionnel mais utile)
- Gaine thermorétractable (protection connexions)

---

## ⚠️ Consignes de sécurité

### **Interdictions absolues**
- ❌ Ne JAMAIS toucher les composants sous tension
- ❌ Ne JAMAIS débrancher le moteur quand le système est alimenté
- ❌ Ne JAMAIS dépasser 28V sur le TMC2209
- ❌ Ne JAMAIS alimenter l'ESP32 > 5.5V
- ❌ Ne JAMAIS court-circuiter +24V et GND

### **Bonnes pratiques**
- ✅ TOUJOURS vérifier les tensions avant de brancher
- ✅ TOUJOURS débrancher avant toute modification
- ✅ TOUJOURS prévoir un interrupteur d'urgence accessible
- ✅ TOUJOURS ventiler le boîtier électronique
- ✅ TOUJOURS avoir un extincteur à proximité (sécurité électrique)

---

## 📊 État d'avancement du câblage

| Composant | Statut | Testé | Notes |
|-----------|--------|-------|-------|
| Alimentation 24V | ✅ Opérationnel | ✅ | Tension stable |
| Buck Converter | ✅ Réglé 5.0V | ✅ | Stabilité OK |
| ESP32 | ✅ Flashé | ✅ | WiFi connecté |
| TMC2209 | ✅ VREF 0.55V | ✅ | Moteur silencieux |
| INA219 | ✅ I²C actif | ✅ | Monitoring préc
