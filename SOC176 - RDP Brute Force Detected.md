# Writeup — Traitement d'alerte SOC

## 1. Informations générales

| Champ                          | Valeur                    |
| ------------------------------ | ------------------------- |
| **ID ticket / alerte**         | 234                       |
| **Date et heure de détection** | Mar, 07, 2024, 11:44 AM   |
| **Analyste**                   | Pogoo                     |
| **Source de détection**        | Security Analyst          |
| **Nom de la règle déclenchée** | RDP Brute Force Detected  |
| **Sévérité**                   | Medium                    |
| **Statut final**               | Vrai positif              |


---

## 2. Contexte technique

- **Asset(s) concerné(s)** : Matthew / 172.16.17.148 / Matthew
- **Description de l'alerte** : Multiples tentatives d'authentification RDP échouées depuis une IP externe unique (218.92.0.56) vers host 172.16.17.148, ciblant plusieurs comptes différents (Matthew, sysadmin, guest, admin) en peu de temps — signature typique brute force. Une tentative finit en succès sur compte Matthew.
- **Résumé de la règle de détection** (logique de déclenchement) : Alerte se déclenche sur volume anormal d'échecs auth RDP (EventId 4625) depuis même source IP/port vers destination port 3389, sur fenêtre de temps courte, avec spray sur plusieurs usernames.

---

## 3. Timeline des événements

| Heure (UTC/local) | Événement | Source | Preuve |
| ----------------- | --------- | ------ | ------ |
| Mar 07 2024, 11:44 AM | Échec logon RDP user "Matthew" | 218.92.0.56 → 172.16.17.148:3389 | EventId 4625, 0xC000006A |
| Mar 07 2024, 11:44 AM | Échec logon RDP user "sysadmin" | 218.92.0.56 → 172.16.17.148:3389 | EventId 4625, 0xC000006A |
| Mar 07 2024, 11:44 AM | Échec logon RDP user "guest" | 218.92.0.56 → 172.16.17.148:3389 | EventId 4625, 0xC000006A |
| Mar 07 2024, 11:44 AM | Échec logon RDP user "admin" | 218.92.0.56 → 172.16.17.148:3389 | EventId 4625, 0xC000006A |
| Mar 07 2024, 11:44 AM | Succès logon RDP user "Matthew" | 218.92.0.56 → 172.16.17.148:3389 | EventId 4624, Logon Type 10 (RemoteInteractive) |

---

## 4. Analyse détaillée

### 4.1 Logs et artefacts analysés
- Log 1:
	- type : OS
	- source_address : 218.92.0.56
	- source_port : 51548
	- destination_address : 172.16.17.148
	- destination_port : 3389
	- time : Mar, 07, 2024, 11:44 AM
	- Username : Matthew
	- EventId : 4625
	- Error Code : 0xC000006A(user name is correct but the password is wrong)

- Log 2:
	- type : OS
	- source_address : 218.92.0.56
	- source_port : 51548
	- destination_address : 172.16.17.148
	- destination_port : 3389
	- time : Mar, 07, 2024, 11:44 AM
	- Username : sysadmin
	- EventId : 4625
	- Error Code : 0xC000006A(user name is correct but the password is wrong)

- Log 3:
	- type : OS
	- source_address : 218.92.0.56
	- source_port : 51548
	- destination_address : 172.16.17.148
	- destination_port : 3389
	- time : Mar, 07, 2024, 11:44 AM
	- Username : guest
	- EventId : 4625
	- Error Code : 0xC000006A(user name is correct but the password is wrong)

- Log 4:
	- type : OS
	- source_address : 218.92.0.56
	- source_port : 51548
	- destination_address : 172.16.17.148
	- destination_port : 3389
	- time : Mar, 07, 2024, 11:44 AM
	- Username : admin
	- EventId : 4625
	- Error Code : 0xC000006A(user name is correct but the password is wrong)

- Log 5:
	- type : OS
	- source_address : 218.92.0.56
	- source_port : 51548
	- destination_address : 172.16.17.148
	- destination_port : 3389
	- time : Mar, 07, 2024, 11:44 AM
	- Username : Matthew
	- EventId : 4624
	- Logon Type : 10(RemoteInteractive)

### 4.2 Requêtes / commandes utilisées
```
LetsDefend TI : 218.92.0.56 -> Malicious
VirusTotal: 218.92.0.56 -> Malicious
```

### 4.4 Indicateurs de compromission (IOC)

| Type | Valeur      | Contexte                                                      |
| ---- | ----------- | ------------------------------------------------------------- |
| IP   | 218.92.0.56 | IP source of brute force finished by success logon on Matthew |


---

## 5. Verdict

**Conclusion :** True Positive

**Justification :**
> IP source 218.92.0.56 classée malveillante par LetsDefend TI et VirusTotal. Log montre spray brute force sur 4 comptes (Matthew, sysadmin, guest, admin) suivi d'un logon RDP réussi (4624, type 10) sur compte Matthew depuis même IP — preuve de compromission effective, pas juste tentative bloquée.

---

## 6. Actions réalisées

- [x] Confinement (isolation host, blocage IP/domaine, désactivation compte)

---

## 7. Recommandations

- **Tuning de la règle** (si faux positif) : N/A, vrai positif — règle fonctionne correctement.
- **Durcissement préventif** :
  - Désactiver RDP direct depuis Internet, passer par VPN ou RDP Gateway.
  - Forcer MFA sur comptes RDP.
  - Politique mots de passe forts + lockout après N échecs (limite brute force).
  - Restreindre port 3389 par firewall aux IP de confiance.
- **Actions de suivi** :
  - Réinitialiser mot de passe compte Matthew.
  - Vérifier activité post-logon sur host (persistence, mouvement latéral, exfiltration).
  - Bloquer IP 218.92.0.56 sur firewall/EDR (si pas déjà fait).
  - Vérifier logs autres hosts pour même source IP.