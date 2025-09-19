# 📄 README — Tiroir Motorisé Silencieux ESP32 + ESPHome  


---

## 🎯 Objectif du projet

Créer un **tiroir motorisé silencieux**, installé **sous ton bureau**, pour ranger ou déployer ton **clavier et ta souris** à la demande.

- Piloté par **ESP32** via **ESPHome** → intégration native dans **Home Assistant**.
- **Silence total** garanti grâce au driver **TMC2209 en mode StealthChop**.
- Contrôle précis : position ajustable à 1%, arrêt automatique en fin de course.
- Sécurité renforcée : capteurs optiques + surveillance de courant (INA219).
- Automatisation possible : “ouvre quand je m’assois”, “ferme à 20h”, etc.

---

## 🧠 Fonctionnement global

1. L’**ESP32** reçoit une commande (bouton, Home Assistant, automatisation).
2. Il pilote le **moteur NEMA 17** via le **driver TMC2209** → mouvement ultra-silencieux.
3. Le mouvement est transmis au tiroir via une **courroie GT2 + poulies 20T**.
4. Les **rails télescopiques** guident le plateau sur 40cm en douceur.
5. Les **capteurs TCRT5000** détectent les positions extrêmes → arrêt automatique.
6. Le module **INA219** surveille la consommation → arrêt d’urgence si blocage.
7. Une **alim 24V** alimente le moteur, un **step-down 24V→5V** alimente l’ESP32.

---

## 📦 Liste de course 

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

## 🧰 Items Bonus Recommandés — Références à chercher

- 🔌 **“Lot 120 câbles Dupont mâle femelle”**
- 📦 **“Boîtier étanche IP65 100x70x50mm”**
- 🚨 **“Bouton arrêt d’urgence 22mm NO NC”**
- 🔩 **“Kit visserie M3 M4 makers 200 pièces”**

---

## 💡 Prochaines étapes (après réception)


➡️ **Schéma de câblage détaillé (GPIO, power, capteurs)**  
➡️ **Code ESPHome complet avec config TMC2209 StealthChop + sécurité**  
➡️ **Guide de montage mécanique (fixation rails, tendeur courroie, calibrage)**


---
