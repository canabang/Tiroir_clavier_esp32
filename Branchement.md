# 🔌 README — Branchements Tiroir Motorisé ESP32

> 📅 Dernière mise à jour : Octobre 2025  
> ✅ Statut : Schéma validé — Prêt pour câblage physique

---

## 🎯 Vue d'ensemble du système

Ce document décrit le **câblage électrique complet** du tiroir motorisé piloté par ESP32 + TMC2209.

### **Architecture générale**
```
Alim 24V 3A
    ├──→ INA219 (mesure courant) → TMC2209 (moteur)
    ├──→ Buck Converter LM2596 → ESP32 (5V)
    └──→ Rail GND commun (toutes les masses)
```

---

## 📦 Matériel utilisé

| Composant | Quantité | Fonction |
|-----------|----------|----------|
| **Alim 24V 3A** | 1 | Alimentation principale |
| **Buck Converter LM2596** | 1 | 24V → 5V pour ESP32 |
| **ESP32 DevKit 38 pins** | 1 | Contrôleur principal |
| **TMC2209 V2.0** | 1 | Driver moteur silencieux |
| **INA219** | 1 | Mesure courant/tension I2C |
| **Moteur NEMA 17** | 1 | Moteur pas-à-pas 42N.cm |

---

<img width="3000" height="2112" alt="schema_tiroir" src="https://github.com/user-attachments/assets/60a2d40f-fe40-4923-bf5d-c460ab53a1ae" />


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
| **GPIO 14** | `STEP` | Signal de pas |
| **GPIO 12** | `DIR` | Direction (avant/arrière) |
| **GPIO 13** | `EN` | Enable moteur (LOW=actif) |

#### **Communication I2C (INA219)**
| Pin ESP32 | Pin INA219 | Fonction |
|-----------|------------|----------|
| **GPIO 21** | `SDA` | I2C Data |
| **GPIO 22** | `SCL` | I2C Clock |

#### **Capteurs fins de course (TCRT5000)** *(À câbler)*
| Pin ESP32 | Capteur | Position |
|-----------|---------|----------|
| **GPIO 18** | TCRT5000 #1 | Fin de course FERMÉ |
| **GPIO 19** | TCRT5000 #2 | Fin de course OUVERT |

#### **Boutons de contrôle** *(À câbler)*
| Pin ESP32 | Fonction | Type |
|-----------|----------|------|
| **GPIO 25** | LED Bouton VERT | Sortie (avec R 220Ω) |
| **GPIO 26** | Bouton VERT | Entrée (INPUT_PULLUP) |
| **GPIO 27** | LED Bouton BLEU | Sortie (avec R 220Ω) |
| **GPIO 32** | Bouton BLEU | Entrée (INPUT_PULLUP) |
| **GPIO 33** | LED Bouton ROUGE | Sortie (avec R 220Ω) |
| **GPIO 35** | Bouton ROUGE | Entrée (INPUT_ONLY) |

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
| **STEP** | ← ESP32 `GPIO 14` | Signal de pas |
| **DIR** | ← ESP32 `GPIO 12` | Direction |
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

## 🔥 Rail GND commun — Point critique

**TOUS les GND doivent être reliés ensemble** pour créer une référence électrique commune.

### **Liste des masses à connecter :**
```
[GND COMMUN]
    ├─ Alim 24V (-)
    ├─ Buck Converter IN-
    ├─ Buck Converter OUT-
    ├─ TMC2209 GND (power) — bornier vis
    ├─ TMC2209 GND (logic) — pin header
    ├─ ESP32 GND
    ├─ INA219 GND
    ├─ Boutons GND (à câbler)
    └─ Capteurs GND (à câbler)
```

### **Méthode recommandée :**
Utiliser un **bornier à vis 8 positions** ou une **breadboard** comme point de distribution GND central.

---

## ⚙️ Réglages AVANT mise sous tension

### **1. Réglage Buck Converter (OBLIGATOIRE)**

```bash
1. Brancher UNIQUEMENT alim 24V + Buck Converter
2. Mesurer tension entre OUT+ et OUT- avec multimètre
3. Tourner potentiomètre (tournevis cruciforme)
4. Ajuster à exactement 5.0V (±0.1V)
5. Débrancher AVANT de connecter l'ESP32
```

> ⚠️ **DANGER** : Une tension > 5.5V **détruirait** l'ESP32 instantanément !

---

### **2. Réglage TMC2209 VREF (OBLIGATOIRE)**

```bash
1. Brancher alim 24V + TMC2209 (moteur DÉBRANCHÉ)
2. Mesurer tension entre potentiomètre et GND
3. Tourner potentiomètre (tournevis cruciforme)
4. Ajuster à 0.55V pour 1A RMS
5. Débrancher et connecter le moteur
```

**Formule** : `VREF = Courant_RMS × 0.5 × Rsense`
- Pour 1A RMS : `VREF = 1.0 × 0.5 × 0.11 = 0.55V`
- Pour 0.8A RMS : `VREF = 0.44V`

> ⚠️ **Ne JAMAIS dépasser 1.0V** → risque de surchauffe moteur !

---

## ✅ Checklist avant premier démarrage

### **Tests de sécurité (multimètre)**

- [ ] **Continuité GND** : Toutes les masses bipent entre elles
- [ ] **Pas de court-circuit** : Résistance entre +24V et GND > 10kΩ
- [ ] **Buck réglé** : Tension sortie = 5.0V (±0.1V)
- [ ] **TMC VREF** : Tension potentiomètre = 0.55V
- [ ] **Polarité alim** : Rouge = +24V, Noir = GND

### **Vérifications visuelles**

- [ ] **Dissipateur TMC2209** : Bien collé sur le chip
- [ ] **Câbles serrés** : Tous les borniers à vis bien serrés
- [ ] **Pas de fils dénudés** : Pas de risque de contact
- [ ] **Moteur connecté** : 4 fils dans le bon ordre

---

## 🚀 Procédure de mise sous tension

### **Étape 1 : Test à vide (sans ESP32, sans moteur)**
```
1. Brancher alim 24V
2. Vérifier LED Buck Converter (rouge allumée)
3. Mesurer 5V entre Buck OUT+ et OUT-
4. Débrancher si OK
```

### **Étape 2 : Test avec ESP32 (sans moteur)**
```
1. Connecter ESP32 au Buck Converter
2. Brancher alim 24V
3. Vérifier LED ESP32 (bleue allumée)
4. Connecter USB et programmer ESPHome
5. Tester GPIO (clignoter une LED test)
```

### **Étape 3 : Test moteur (à vide)**
```
1. Connecter moteur NEMA 17 au TMC2209
2. Brancher alim 24V
3. Envoyer commande STEP depuis ESPHome
4. Vérifier rotation silencieuse
5. Vérifier mesure INA219 (~0.5-1A)
```

### **Étape 4 : Test avec charge (tiroir monté)**
```
1. Installer système sur le tiroir
2. Tester ouverture/fermeture lente
3. Ajuster vitesse dans ESPHome
4. Calibrer fins de course
```

---

## 🔧 Dépannage rapide

| Problème | Cause probable | Solution |
|----------|----------------|----------|
| ESP32 ne s'allume pas | Buck mal réglé ou GND absent | Vérifier 5V + continuité GND |
| Moteur ne tourne pas | TMC2209 EN mal câblé | Vérifier GPIO 13 → LOW |
| Moteur chauffe | VREF trop élevé | Réduire à 0.5V |
| INA219 affiche 0A | VIN+/VIN- mal câblés | Vérifier série sur ligne 24V |
| TMC2209 chauffe | Pas de dissipateur | Coller dissipateur thermique |
| Mouvement saccadé | Alimentation insuffisante | Vérifier alim 24V 3A |

---

## 📋 Composants à câbler ultérieurement

### **Boutons de contrôle physique (3x)**
- 🟢 **Bouton VERT** : Ouvrir (GPIO 25 LED + GPIO 26 bouton)
- 🔵 **Bouton BLEU** : Fermer (GPIO 27 LED + GPIO 32 bouton)
- 🔴 **Bouton ROUGE** : Stop (GPIO 33 LED + GPIO 35 bouton)
- Résistances 220Ω sur chaque LED (obligatoire)

### **Capteurs fins de course (2x)**
- 📏 **TCRT5000 #1** : Position FERMÉ (GPIO 18)
- 📏 **TCRT5000 #2** : Position OUVERT (GPIO 19)
- Alimentés en 3.3V + réglage potentiomètre

---

## 📚 Documentation technique

### **Datasheets**
- [TMC2209 Datasheet](https://www.trinamic.com/products/integrated-circuits/details/tmc2209-la/)
- [INA219 Datasheet](https://www.ti.com/lit/ds/symlink/ina219.pdf)
- [LM2596 Datasheet](https://www.ti.com/lit/ds/symlink/lm2596.pdf)

### **Outils recommandés**
- Multimètre digital (mesure tension/continuité)
- Tournevis cruciforme petit (réglage potentiomètres)
- Pince coupante + dénude-fil
- Fer à souder (si nécessaire)

---

## ⚠️ Consignes de sécurité

- ❌ Ne JAMAIS toucher les composants sous tension
- ❌ Ne JAMAIS débrancher le moteur quand le système est alimenté
- ❌ Ne JAMAIS dépasser 28V sur le TMC2209
- ❌ Ne JAMAIS alimenter l'ESP32 > 5.5V
- ✅ TOUJOURS vérifier les tensions avant de brancher
- ✅ TOUJOURS débrancher avant toute modification
- ✅ TOUJOURS prévoir un interrupteur d'urgence



> ✅ **Statut du câblage** : Schéma validé — Prêt pour câblage physique  
> 📅 **Dernière vérification** : Octobre 2025
