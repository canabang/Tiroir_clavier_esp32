# 📄 README — Tiroir Motorisé Silencieux ESP32 + ESPHome  
## ✅ Projet "Tiroir Clavier/Souris Intelligent" — Phase 2 en cours

> 🛠️ Conçu pour être silencieux, sécurisé, pilotable depuis Home Assistant, et contrôlable via 3 boutons physiques LED (Ouvrir / Fermer / Stop)

---

## 🎯 Objectif du projet

Créer un **tiroir motorisé**, installé **sous ton bureau**, pour ranger ou déployer ton **clavier et ta souris** :

- ✅ **Silencieux** → grâce au driver **TMC2209 en mode StealthChop**.
- ✅ **Précis** → position contrôlable à 1% via ESPHome (`cover` entity).
- ✅ **Sécurisé** → capteurs de fin de course + détection de surintensité (INA219).
- ✅ **Contrôlable physiquement** → 3 boutons LED :  
  - 🟢 **Vert = Déployer**  
  - 🔵 **Bleu = Rentrer**  
  - 🔴 **Rouge = Stop**  
- ✅ **LED contrôlables** → s'éteignent automatiquement ou via automatisation HA (ex: la nuit).
- ✅ **Automatisable** → "ouvre quand je m'assois", "ferme à 20h", etc.

---

## 🧠 Fonctionnement global

1. L'**ESP32** reçoit une commande (bouton physique, Home Assistant, ou automatisation).
2. Il pilote le **moteur NEMA 17** via le **driver TMC2209** → mouvement ultra-silencieux.
3. Le mouvement est transmis au tiroir via une **courroie GT2 + poulies 20T**.
4. Les **rails télescopiques** guident le plateau sur **40cm** en douceur.
5. Les **capteurs TCRT5000** détectent les positions extrêmes → arrêt automatique.
6. Le module **INA219** surveille la consommation → arrêt d'urgence si blocage.
7. Une **alim 24V** alimente le moteur, un **step-down 24V→5V** alimente l'ESP32.
8. Les **3 boutons LED** permettent un contrôle manuel intuitif → feedback visuel immédiat.

---

## 📦 Liste de course 

---

### 1. 🖥️ ESP32 DevKit USB-C  
🔗 https://fr.aliexpress.com/item/1005006212080137.html

---

### 2. ⚙️ Moteur pas à pas NEMA 17 (42N.cm)  
🔗 https://fr.aliexpress.com/item/32665922113.html

---

### 3. 🔇 Driver TMC2209 V2.0 (UART, StealthChop)  
🔗 https://fr.aliexpress.com/item/1005004063785803.html

---

### 4. 🔄 Kit Courroie GT2 + Poulies 20T (2m)  
🔗 https://fr.aliexpress.com/item/32303569726.html  
✅ **Poulies 20T, alésage 5mm, double flasque — parfait pour NEMA17**

---

### 5. 🛤️ Rails télescopiques 400mm (paire)  
🔗 https://fr.aliexpress.com/item/1005002992085234.html

---

### 6. 📏 Capteurs optiques TCRT5000 (lot de 5)  
🔗 https://fr.aliexpress.com/item/1005009053525333.html

---

### 7. 🔌 Module INA219 I2C (mesure courant/tension)  
🔗 https://fr.aliexpress.com/item/1005007533576335.html

---

### 8. 🔋 Alimentation 24V 3A (EU, avec câble)  
🔗 https://fr.aliexpress.com/item/1005007087317638.html

---

### 9. 🔌 Régulateur DC-DC Step-Down (24V → 5V)  
🔗 https://fr.aliexpress.com/item/1005006149348817.html

---

### 10. 🔘 Boutons poussoirs 12mm LED (x3 — vert, bleu, rouge)  
🔗 https://fr.aliexpress.com/item/1005003575736338.html  
✅ **Momentané, 3-9V, self-reset — parfait avec résistance 220Ω**

---

### 11. 🔌 Boîte de résistances 220Ω (pour les LED)  
🔗 https://fr.aliexpress.com/item/1005008902058496.html  
✅ **Lot de 100 résistances 220Ω 1/4W — indispensable pour contrôler la luminosité en 3.3V**

---

## 🧰 Items Bonus Recommandés — Références à chercher

> Pas de lien — cherche simplement ces références exactes.

- 🔌 **"Lot 120 câbles Dupont mâle femelle"**
- 📦 **"Boîtier étanche IP65 100x70x50mm"**
- 🔩 **"Kit visserie M3 M4 makers 200 pièces"**
- 📏 **"Plaque montage boutons 12mm aluminium noire 3 trous"** (optionnel — design pro)

---

## 🚀 État d'avancement du projet

### ✅ Phase 1 : Câblage & Tests de base — **TERMINÉE**

- [x] Alimentation 24V → Buck Converter → ESP32 (5V stable)
- [x] TMC2209 câblé + VREF réglé à 0.55V (1A RMS)
- [x] INA219 en série sur ligne 24V (monitoring actif)
- [x] ESP32 flashé avec ESPHome
- [x] Entité `cover` fonctionnelle dans Home Assistant
- [x] Tests de rotation horaire/anti-horaire validés
- [x] Protection surintensité active (arrêt à 1.5A)
- [x] Moteur NEMA 17 opérationnel (silencieux via TMC2209)

**✨ Résultat** : Le système motorisé est **100% opérationnel** via Home Assistant

---

### 🔧 Phase 2 : Boutons physiques & Capteurs — **EN COURS**

#### **À câbler :**

**🔘 Boutons LED (3x)**
- 🟢 **GPIO 25** → LED Bouton VERT (via résistance 220Ω)
- 🟢 **GPIO 26** → Bouton VERT (INPUT_PULLUP)
- 🔵 **GPIO 27** → LED Bouton BLEU (via résistance 220Ω)
- 🔵 **GPIO 32** → Bouton BLEU (INPUT_PULLUP)
- 🔴 **GPIO 33** → LED Bouton ROUGE (via résistance 220Ω)
- 🔴 **GPIO 35** → Bouton ROUGE (INPUT_ONLY)

**📏 Capteurs fins de course (2x TCRT5000)**
- 📍 **GPIO 18** → Capteur position FERMÉ
- 📍 **GPIO 19** → Capteur position OUVERT
- Alimentés en **3.3V** (ESP32) + réglage potentiomètre de sensibilité

#### **À développer dans le code :**
- [ ] Logique de contrôle des boutons (press → action)
- [ ] Feedback LED (allumage temporaire 1s)
- [ ] Extinction auto des LED après action
- [ ] Intégration capteurs fins de course (arrêt automatique)
- [ ] Procédure de homing (calibration position initiale)
- [ ] Mode nuit (désactivation LED via switch virtuel)

---

### 🎯 Phase 3 : Finalisation mécanique — **À VENIR**

- [ ] Installation rails télescopiques sous bureau
- [ ] Montage poulie motrice sur axe NEMA 17
- [ ] Installation courroie GT2 + poulie folle (tensionneur)
- [ ] Fixation plateau tiroir (clavier + souris)
- [ ] Montage capteurs TCRT5000 sur positions extrêmes
- [ ] Positionnement boîtier boutons (accès facile)
- [ ] Tests de charge réelle (poids clavier + souris)
- [ ] Calibration finale (vitesse, accélération, positions)

---

### 🤖 Phase 4 : Automatisations Home Assistant — **À VENIR**

- [ ] Ouverture automatique (capteur de présence bureau)
- [ ] Fermeture nocturne (20h ou détection absence)
- [ ] Intégration géolocalisation (ferme si départ maison)
- [ ] Scénarios vocaux (Alexa/Google Home)
- [ ] Dashboard dédié avec historique d'usage
- [ ] Notifications push (blocage, position anormale)

---

## 💡 Fonctionnalités logicielles ESPHome (actuelles)

### ✅ Déjà implémenté

✅ Entité `cover` dans Home Assistant → ouvrir/fermer à X%  
✅ Contrôle de vitesse (800 steps/s) + accélération douce (400 steps/s²)  
✅ Monitoring courant moteur en temps réel (INA219)  
✅ Protection surintensité (arrêt d'urgence à 1.5A)  
✅ Boutons de test rotation (interface ESPHome)  
✅ Reset position manuelle (recalibration)  
✅ Restauration position après reboot  

### 🔜 À venir (Phase 2)

- LED allumée à l'appui → feedback visuel  
- LED éteinte automatiquement après 1s → pas de pollution lumineuse  
- LED désactivables globalement via interrupteur virtuel (ex: la nuit)  
- Détection d'obstacle → arrêt + log + notification HA  
- Calibration automatique via capteurs fins de course  

---

## 🔧 Prochaines étapes prioritaires

### 1. **Câblage boutons LED (30 min)**
```
Matériel nécessaire :
- 3 boutons LED 12mm (vert, bleu, rouge)
- 3 résistances 220Ω
- Câbles Dupont mâle-femelle
- Fil rigide ou breadboard pour GND commun
```

### 2. **Installation capteurs TCRT5000 (1h)**
```
Procédure :
1. Alimenter en 3.3V + GND commun
2. Régler potentiomètre (détection distance ~5mm)
3. Tester sortie digitale (HIGH = détecté, LOW = absent)
4. Positionner physiquement sur tiroir (repères blancs)
5. Connecter GPIO 18 (fermé) + GPIO 19 (ouvert)
```

### 3. **Adaptation code ESPHome (2h)**
```yaml
# Ajouter :
- binary_sensor pour boutons (on_press actions)
- output pour LED (contrôle PWM si besoin)
- binary_sensor pour TCRT5000 (fin de course)
- automation pour homing au démarrage
- timer pour extinction LED après 1s
```

### 4. **Tests de sécurité finaux**
- [ ] Vérifier arrêt immédiat sur fin de course
- [ ] Tester protection surintensité (bloquer manuellement)
- [ ] Valider comportement en cas de coupure secteur
- [ ] Confirmer vitesse acceptable (pas de vibrations)

---

## 📊 Calibration nécessaire

### **Variable critique à ajuster :**
```yaml
steps_per_100percent = 10000  # VALEUR À MESURER
```

**Procédure de calibration :**
1. Positionner tiroir complètement fermé
2. Compter manuellement les pas pour course complète (40cm)
3. Calculer : `steps = distance_mm × (steps_par_tour / diamètre_poulie_mm)`
4. Exemple avec poulie 20T (Ø12.73mm) :
   - 1 tour = 200 pas × 8 microsteps = 1600 steps
   - 1 tour = π × 12.73mm ≈ 40mm déplacement
   - 400mm = 16000 steps → `steps_per_100percent = 16000`

---

## 🛡️ Sécurité & Fiabilité

### **Protections actives**
- ✅ Surintensité : Arrêt à 1.5A (seuil configurable)
- ✅ Désactivation moteur : Auto après 500ms d'inactivité
- 🔜 Fins de course : Arrêt matériel instantané (Phase 2)
- 🔜 Timeout mouvement : Arrêt si durée anormale (Phase 2)

### **Points de vigilance**
- ⚠️ Ne JAMAIS forcer le tiroir manuellement quand alimenté
- ⚠️ Vérifier régulièrement la tension de la courroie
- ⚠️ Surveiller température TMC2209 (dissipateur obligatoire)
- ⚠️ Nettoyer régulièrement les capteurs TCRT5000 (poussière)

---

## 📚 Documentation technique

### **Fichiers du projet**
- `Branchement.md` → Schéma câblage complet + procédures
- `esp-proto-tiroir.yaml` → Configuration ESPHome actuelle
- `README.md` → Ce document (vue d'ensemble + roadmap)

### **Datasheets**
- [TMC2209 Datasheet](https://www.trinamic.com/products/integrated-circuits/details/tmc2209-la/)
- [INA219 Datasheet](https://www.ti.com/lit/ds/symlink/ina219.pdf)
- [LM2596 Datasheet](https://www.ti.com/lit/ds/symlink/lm2596.pdf)
- [TCRT5000 Datasheet](https://www.vishay.com/docs/83760/tcrt5000.pdf)

### **Ressources ESPHome**
- [Cover Component](https://esphome.io/components/cover/template.html)
- [Stepper Component](https://esphome.io/components/stepper/a4988.html)
- [INA219 Sensor](https://esphome.io/components/sensor/ina219.html)

---

## 🎉 État global du projet

| Phase | Avancement | Statut |
|-------|-----------|--------|
| 🔌 Câblage électrique | 80% | ✅ Opérationnel (manque boutons + capteurs) |
| 💻 Code ESPHome | 60% | ✅ Fonctionnel (à enrichir Phase 2) |
| 🔧 Mécanique | 0% | ⏳ En attente pièces |
| 🤖 Automatisations | 0% | ⏳ Phase 4 |

**🚀 Prochaine étape immédiate** : Câbler les 3 boutons LED + 2 capteurs TCRT5000 puis adapter le code ESPHome.

---

> ✅ **Dernière mise à jour** : Octobre 2025  
> 📧 **Support** : Voir documentation technique pour références  
> 🔧 **Maintenance** : Vérification mensuelle tensions + nettoyage capteurs
