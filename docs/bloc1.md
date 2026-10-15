# Réalisations Techniques - Bloc 1 (Support & Services)

!!! abstract "Périmètre de l'Épreuve E5 - Bloc 1"
    Ce volet documente les activités relatives à la **gestion du patrimoine informatique**, au **traitement des incidents / demandes d'assistance**, et à l'**accompagnement des utilisateurs** dans l'appropriation des outils numériques.

---

## Fiche Projet : Déploiement d'une Solution de Gestion de Parc & Helpdesk (GLPI)

### 1. Contexte & Problématique Métier
* **Cadre :** Atelier Professionnel (Centre de formation ESPL) / Projet d'entreprise
* **Période :** Année 2026-2027
* **Constat initial :** L'organisation gère son parc informatique et ses demandes d'assistance de manière informelle (fichiers tableurs non synchronisés, sollicitations par e-mail ou couloir).
* **Objectifs :**
    - Centraliser l'inventaire matériel et logiciel de l'infrastructure.
    - Mettre en place un guichet unique de ticketing avec suivi des délais d'intervention (SLA).
    - Automatiser la remontée des configurations des postes de travail.

---

### 2. Compétences Référentiel Mobilisées
* **B1.1 - Gérer le patrimoine informatique :** Recensement automatisé des équipements, gestion des licences et suivi des garanties.
* **B1.2 - Répondre aux incidents et aux demandes d'assistance :** Qualification du besoin, hiérarchisation des urgences, communication et résolution.
* **B1.4 - Travailler en mode projet :** Définition des étapes de déploiement, rédaction de guides utilisateurs et conduite du changement.
* **B1.5 - Mettre à disposition des utilisateurs un service informatique :** Déploiement de la plateforme GLPI et configuration des profils d'accès.

---

### 3. Architecture Fonctionnelle & Rôles
[ Utilisateurs / Clients ]  --> ( Portail Web GLPI / Email )
|
v
[ Postes & Serveurs ]       --> [ Serveur GLPI + Base MariaDB ]
( Agents GLPI auto )                 |
v
[ Équipe Support / Admin ]
| Rôle / Profil GLPI | Périmètre d'action | Droits associés |
| :--- | :--- | :--- |
| **Self-Service (Utilisateur)** | Déclarer un incident, suivre l'état de ses demandes | Lecture restreinte, ouverture de tickets |
| **Technicien (Support N1/N2)** | Prendre en charge les tickets, documenter la résolution | Attribution, modification, enrichissement de la base de connaissances |
| **Super-Admin** | Administration système, configuration des connecteurs LDAP/AD | Gestion complète de la plateforme et des droits |

---

### 4. Déploiement Technique & Configuration

=== "Installation & Base de Données (Linux Debian)"
    ```bash
    # Installation du serveur LAMP (Apache, MariaDB, PHP)
    sudo apt update && sudo apt install -y apache2 mariadb-server php php-mysql php-curl php-gd php-mbstring php-xml php-intl

    # Sécurisation et initialisation de la base de données GLPI
    sudo mysql -u root
    CREATE DATABASE db_glpi CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
    CREATE USER 'glpi_user'@'localhost' IDENTIFIED BY 'MotDePasseSecurise123!';
    GRANT ALL PRIVILEGES ON db_glpi.* TO 'glpi_user'@'localhost';
    FLUSH PRIVILEGES;
    EXIT;
    ```

=== "Automatisation de l'Inventaire (Agent GLPI)"
    ```bash
    # Déploiement de l'agent sur un poste client Windows / Linux
    # Configuration du point de contact vers le serveur GLPI
    glpi-agent --server [http://192.168.20.10/glpi/front/inventory.php](http://192.168.20.10/glpi/front/inventory.php) --runnow
    ```

---

### 5. Procédure de Traitement d'un Incident (Cycle de Vie)

!!! tip "Méthodologie ITIL appliquée au Support"
    1. **Prise en charge & Diagnostic :** Qualification de l'incident (impact / urgence), attribution d'une priorité et accusé de réception à l'utilisateur.
    2. **Investigation technique :** Reproduction du problème, vérification des logs et application d'un correctif ou d'une solution de contournement.
    3. **Communication utilisateur :** Explication claire des actions menées sans jargon excessif.
    4. **Clôture & Capitalisation :** Validation avec le demandeur et rédaction d'une fiche solution dans la base de connaissances interne.

---

### 6. Bilan & Indicateurs Clés (KPI)
- **Inventaire :** 100 % des machines du lab remontées automatiquement avec leurs caractéristiques (CPU, RAM, OS, logiciels installés).
- **Temps moyen de prise en charge :** Réduction du délai grâce aux notifications automatiques par mail.
- **Capitalisation :** Création de 5 procédures types dans la base de connaissances pour traiter les pannes récurrentes (imprimantes, profils corrompus, accès VPN).