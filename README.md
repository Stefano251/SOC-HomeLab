## SOC-HomeLab
Work in Progress | Home Lab SOC con Wazuh, pfSense+Suricata, TheHive. Architettura virtuale per simulazione ambienti aziendali e incident response.

## Descrizione
Home Lab personale progettato per simulare un ambiente SOC (Security Operations Center) aziendale in scala ridotta.
L'obiettivo è acquisire esperienza pratica su strumenti reali utilizzati in ambienti SOC, documentando ogni fase del processo per dimostrare competenze concrete in ambito Blue Team e Incident Response.

## Architettura del Lab
[Kali Linux — PC Attaccante]
|
[Internet]
|
[pfSense 2.8.0 + Suricata]
WAN: 192.168.1.56 | LAN: 192.168.100.1
Firewall + IDS/IPS
|
[LAN 192.168.100.0/24]
|
┌─────────┼──────────────┐
|         |              |
[Win10]  [Win7]     [Ubuntu Server 24.04]
Wazuh    Wazuh       Wazuh Agent
Agent    Agent       + Auditd
+Sysmon  +Sysmon
|         |              |
└─────────┴──────────────┘
|
[Wazuh Manager 4.x]
IP: 192.168.1.57
SIEM + EDR + XDR
|
[TheHive + Cortex]
SOAR — Case Management
(in fase di installazione)
[PC Fisso Host]
Wazuh Agent + Sysmon

## Strumenti Utilizzati
| Categoria | Strumento | Ruolo |
| SIEM/EDR/XDR | Wazuh | Monitoraggio, detection, alerting |
| Firewall/IDS/IPS | pfSense + Suricata | Controllo traffico e rilevamento intrusioni |
| Endpoint Monitoring | Sysmon (Windows) | Log avanzati processi e rete |
| Endpoint Monitoring | Auditd (Linux) | Syscall e log di sistema Linux |
| SOAR | TheHive + Cortex | Case management e incident response |
| Attacker Machine | Kali Linux | Simulazione attacchi |
| Virtualizzazione | Oracle VirtualBox | Gestione VM |

## Macchine Virtuali
| VM | OS | Ruolo | IP |
| pfSense | FreeBSD | Firewall + IDS/IPS | 192.168.1.56 (WAN) |
| Dipendente 1 | Windows 10 | Endpoint aziendale | DHCP 192.168.100.x |
| Dipendente 2 | Windows 7 | Endpoint legacy vulnerabile | DHCP 192.168.100.x |
| Server Aziendale | Ubuntu Server 24.04 | Server interno | 192.168.100.103 |
| Wazuh Manager | Ubuntu | SIEM/EDR/XDR | 192.168.1.57 |
| TheHive | Ubuntu Server | SOAR | In installazione |
| Attaccante | Kali Linux | Red Team simulato | PC portatile |

## Roadmap
- [x] Installazione e configurazione pfSense + LAN
- [x] Wazuh Manager installato e raggiungibile
- [x] Wazuh Agent + Sysmon su Windows 10
- [x] Wazuh Agent + Sysmon su Windows 7
- [x] Wazuh Agent + Auditd su Ubuntu Server
- [x] Wazuh Agent + Sysmon su PC Host fisico
- [ ] Configurazione Suricata su pfSense
- [ ] Installazione TheHive + Cortex
- [ ] Integrazione Wazuh → TheHive
- [ ] Simulazioni di attacco con Kali Linux
- [ ] Documentazione alert e incident response
- [ ] Write-up scenari di attacco/difesa

## Disclaimer
Questo lab è costruito esclusivamente a scopo educativo e di apprendimento personale.
Tutte le simulazioni di attacco vengono eseguite in ambiente isolato e controllato.

## Lessons Learned

Questa sezione raccoglie i problemi reali affrontati durante la costruzione del lab, perché anche gli errori fanno parte del percorso di apprendimento.

1. L'importanza degli snapshot

Durante la costruzione del lab è saltata la corrente mentre le VM erano in esecuzione.
Risultato: file system corrotti, pfSense e Wazuh irrecuperabili, tutto da reinstallare da zero.
Lezione imparata: fare uno snapshot ad ogni step significativo è fondamentale,
esattamente come un backup in un ambiente di produzione reale.

2. Compatibilità su sistemi legacy (Windows 7)

Windows 7 rappresenta nel lab un endpoint aziendale legacy volutamente vulnerabile.
Tuttavia ha richiesto attenzione nella scelta delle versioni dei software:
Wazuh Agent, Firefox e altri tool richiedono versioni specifiche per girare
su un OS fuori supporto. Esperienza utile per capire le sfide reali
della gestione di sistemi legacy in azienda.

3. Ubuntu Server 24.04 minimal — comandi mancanti

La versione minimal di Ubuntu Server 24.04 non include comandi base come
ping e wget preinstallati. Questo ha rallentato il collegamento a Wazuh
e la verifica della connettività di rete. Soluzione: installazione manuale
dei pacchetti mancanti tramite apt dopo aver verificato la connettività con curl.
