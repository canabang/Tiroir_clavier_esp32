# 📄 README — Tiroir Motorisé Silencieux ESP32 + ESPHome  
## ✅ Projet “Tiroir Clavier/Souris Intelligent” — Liste de course + Fonctionnalités

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
- ✅ **LED contrôlables** → s’éteignent automatiquement ou via automatisation HA (ex: la nuit).
- ✅ **Automatisable** → “ouvre quand je m’assois”, “ferme à 20h”, etc.

---

## 🧠 Fonctionnement global

1. L’**ESP32** reçoit une commande (bouton physique, Home Assistant, ou automatisation).
2. Il pilote le **moteur NEMA 17** via le **driver TMC2209** → mouvement ultra-silencieux.
3. Le mouvement est transmis au tiroir via une **courroie GT2 + poulies 20T**.
4. Les **rails télescopiques** guident le plateau sur **40cm** en douceur.
5. Les **capteurs TCRT5000** détectent les positions extrêmes → arrêt automatique.
6. Le module **INA219** surveille la consommation → arrêt d’urgence si blocage.
7. Une **alim 24V** alimente le moteur, un **step-down 24V→5V** alimente l’ESP32.
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

- 🔌 **“Lot 120 câbles Dupont mâle femelle”**
- 📦 **“Boîtier étanche IP65 100x70x50mm”**
- 🔩 **“Kit visserie M3 M4 makers 200 pièces”**
- 📏 **“Plaque montage boutons 12mm aluminium noire 3 trous”** (optionnel — design pro)

---

## 💡 Fonctionnalités logicielles ESPHome (à venir)

✅ Entité `cover` dans Home Assistant → ouvrir/fermer à X%  
✅ LED allumée à l’appui → feedback visuel  
✅ LED éteinte automatiquement après 1s → pas de pollution lumineuse  
✅ LED désactivables globalement via interrupteur virtuel (ex: la nuit)  
✅ Détection d’obstacle → arrêt + log  
✅ Automatisations : géolocalisation, heure, capteur de présence...

---

