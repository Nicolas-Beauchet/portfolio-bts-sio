# Réalisations Techniques - Bloc 2 (SISR)

!!! abstract "Périmètre de l'Épreuve E5 - Bloc 2"
    Ce volet documente les projets d'administration, de sécurisation et de déploiement des infrastructures systèmes et réseaux réalisés en centre de formation et en milieu professionnel.

---

## Fiche Projet : Déploiement d'une Infrastructure Réseau & Services Sécurisés

### 1. Contexte & Expression du Besoin
* **Cadre :** Atelier Professionnel (Centre de formation ESPL)
* **Période :** Année 2026-2027
* **Problématique :** Une PME multi-sites doit segmenter son réseau local pour isoler les flux d'administration, les serveurs de production et les postes clients, tout en garantissant un contrôle d'accès strict et un service DNS/DHCP résilient.

---

### 2. Compétences Référentiel Mobilisées
* **B2.1 - Concevoir une solution d'infrastructure réseau :** Découpage IP (VLSM), plan de segmentation VLAN.
* **B2.2 - Installer, tester et déployer une infrastructure :** Configuration d'un serveur Linux (Debian) / Windows Server et routage inter-VLAN.
* **B2.3 - Exploiter, dépanner et sécuriser les services :** Règles de filtrage (pare-feu), contrôle des flux et gestion des journaux.

---

### 3. Architecture & Plan d'Adressage

| Réseau / Rôle | VLAN ID | Plage Réseau | Passerelle | Objectif de Sécurité |
| :--- | :---: | :---: | :---: | :--- |
| **Administration** | 10 | 192.168.10.0/24 | 192.168.10.254 | Accès restreint (SSH, RDP, Webmin) |
| **Serveurs (DMZ / Prod)** | 20 | 192.168.20.0/24 | 192.168.20.254 | Services isolés, ports stricts |
| **Utilisateurs / Postes** | 30 | 192.168.30.0/24 | 192.168.30.254 | Navigation filtrée, isolation des flux |

---

### 4. Mise en Œuvre & Configurations Clés

=== "Configuration Réseau & Interfaces"
    ```bash
    # Exemple d'attribution d'adresses statiques sous Linux (/etc/network/interfaces)
    auto ens18
    iface ens18 inet static
        address 192.168.10.10/24
        gateway 192.168.10.254
        dns-nameservers 192.168.10.1
    ```

=== "Filtrage & Sécurisation (Pare-feu)"
    ```bash
    # Politique par défaut : refus de tout flux non sollicité
    sudo nft add chain inet filter input { type filter hook input priority 0 \; policy drop \; }
    sudo nft add rule inet filter input ct state established,related accept
    sudo nft add rule inet filter input iif "lo" accept
    # Autorisation SSH restreinte au réseau d'administration
    sudo nft add rule inet filter input ip saddr 192.168.10.0/24 tcp dport 22 accept
    ```

---

### 5. Cahier de Recette & Validation
!!! tip "Protocole de Test"
    - **Test de connectivité :** Validation du routage inter-VLAN via tests `ping` et analyse des requêtes ICMP sous Wireshark.
    - **Test de sécurité :** Scan de ports avec `nmap` depuis le VLAN utilisateurs pour attester du blocage des ports d'administration.
    - **Résultat :** Flux autorisés opérationnels, accès non autorisés bloqués et tracés dans les logs systèmes.