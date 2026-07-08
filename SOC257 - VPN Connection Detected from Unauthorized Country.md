# Writeup — Traitement d'alerte SOC

## 1. Informations générales

| Champ | Valeur |
|---|---|
| **ID ticket / alerte** | 225 |
| **Date et heure de détection** | Feb, 13, 2024, 02:04 AM |
| **Analyste** | Pogoo |
| **Source de détection** |
Security Analyst |
| **Nom de la règle déclenchée** | VPN Connection Detected from Unauthorized Country |
| **Sévérité** | Low |
| **Statut final** | Bénin confirmé |

---

## 2. Contexte technique

- **Asset(s) concerné(s)** : Monica / VPN / monica@letsdefend.io
- **Description de l'alerte** : Connexion au VPN avec l'utilisateur Monica
- **Résumé de la règle de détection** (logique de déclenchement) :

---

## 3. Timeline des événements

| Heure (UTC/local) | Événement | Source | Preuve |
|---|---|---|---|
| Feb, 13, 2024, 02:01 AM | Incorrect OTP Code | 113.161.158.12 | Log Management |
| Feb, 13, 2024, 02:02 AM | Incorrect OTP Code | 113.161.158.12 | Log Management |
| Feb, 13, 2024, 02:03 AM | Incorrect OTP Code | 113.161.158.12 | Log Management |

---

## 4. Analyse détaillée

### 4.1 Logs et artefacts analysés
- Log 1:
  - type : Firewall
  - source_address : 113.161.158.12
  - source_port : 48075
  - destination_address : 33.33.33.33
  - destination_port : 443
  - time : Feb, 13, 2024, 02:01 AM
  - Dest : vpn-letsdefend.io
  - User : monica@letsdefend.io
  - Action : Incorrect OTP Code

- Log 2:
  - type : Firewall
  - source_address : 113.161.158.12
  - source_port : 48075
  - destination_address : 33.33.33.33
  - destination_port : 443
  - time : Feb, 13, 2024, 02:02 AM
  - Dest : vpn-letsdefend.io
  - User : monica@letsdefend.io
  - Action : Incorrect OTP Code

- Log 3:
  - type : Firewall
  - source_address : 113.161.158.12
  - source_port : 48075
  - destination_address : 33.33.33.33
  - destination_port : 443
  - time : Feb, 13, 2024, 02:03 AM
  - Dest : vpn-letsdefend.io
  - User : monica@letsdefend.io
  - Action : Incorrect OTP Code

### 4.2 Requêtes / commandes utilisées
```
Log Management: Source Address contains "113.161.158.12"
```

```
AbuseCH: 113.161.158.12 -> Confirme IP use for Brute Force
```

### 4.3 Pivots effectués
- Aucun

### 4.4 Indicateurs de compromission (IOC)

| Type | Valeur | Contexte |
|---|---|---|
| IP | 113.161.158.12 | IP utilisé pour se connécter pour 3 fails et 0 réussite |
| User | Monica | Utilisateur avec mot de passe compromis |

---

## 5. Verdict

**Conclusion :** Benign True Positive

**Justification :**
> Plusieur tentative de connexion avec un echec de MFA, sans echec indiqué pour le mot de passe. Aucune tentative réussie.

---

## 6. Actions réalisées

- [ ] Notification des parties prenantes
- [ ] Surveiller/Bloquer l'addresse IP : 113.161.158.12

---

## 7. Recommandations

- **Durcissement préventif** : Modification du mot de passe de l'utilisateur compromis.