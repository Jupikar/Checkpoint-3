# Administration Système Windows

## Sommaire
1. [Gestion des utilisateurs](#1-gestion-des-utilisateurs)
2. [Restriction utilisateurs](#2-restriction-utilisateurs)
3. [Lecteurs réseaux](#3-lecteurs-réseaux)

## 1. Gestion des utilisateurs

### Q.1.1.1 Création de l'utilisateur Lionel Lemarchand
1. Ouvrir "Utilisateurs et ordinateurs Active Directory"
2. Localiser l'OU où se trouve Kelly Rhameur
3. Clic droit → Copier 
4. Remplir les informations :
   - Prénom : Lionel
   - Nom : Lemarchand

   
![Capture d’écran 2025-03-07 092537](https://github.com/user-attachments/assets/abd77761-4b4a-4882-85c8-8194d909c298)

![Capture d’écran 2025-03-07 092638](https://github.com/user-attachments/assets/b5ae685f-545c-4d3b-8fff-dcdd5a87302f)

![Capture d’écran 2025-03-07 092658](https://github.com/user-attachments/assets/ee8a8ae3-7bf9-4c93-bde0-031cfc72520d)


### Q.1.1.2 Déplacement du compte Kelly Rhameur
1. Créer une nouvelle OU "DeactivatedUsers"
   - Clic droit sur le domaine → Nouveau → Unité d'organisation
   - Nommer "DeactivatedUsers"
2. Désactiver le compte de Kelly Rhameur
   - Clic droit sur son compte → Désactiver

![Capture d’écran 2025-03-07 094214](https://github.com/user-attachments/assets/3366af02-a222-4c38-b204-70c510ee53bb)



3. Déplacer le compte dans l'OU "DeactivatedUsers"
   - Glisser-déposer ou couper-coller


![Capture d’écran 2025-03-07 094424](https://github.com/user-attachments/assets/b796f47e-a42c-4e7f-b5d3-b7dacd1d76b4)


### Q.1.1.3 Modification du groupe
1. Mettre à jour les membres du groupe
2. Retirer Kelly Rhameur
3. Ajouter Lionel Lemarchand

![Capture d’écran 2025-03-07 094534](https://github.com/user-attachments/assets/a7940e8c-d024-40a9-b14f-b59f58c1ab7f)

![Capture d’écran 2025-03-07 094557](https://github.com/user-attachments/assets/b6a753eb-4639-4b31-8b83-6a1b098524ad)


### Q.1.1.4 Gestion des dossiers
1. Créer le dossier pour Lionel Lemarchand
2. Renommer le dossier de Kelly Rhameur :
  
![Capture d’écran 2025-03-07 095747](https://github.com/user-attachments/assets/95977788-c77d-4211-bb54-cc5bbc9dd48d)

## 2. Restriction utilisateurs

### Q.1.2.1 Restriction horaire pour Gabriel Ghul
1. Dans "Utilisateurs et ordinateurs Active Directory"
2. Propriétés du compte de Gabriel Ghul
3. Onglet "Compte" → "Heures de connexion"
4. Configurer :
   - Jours : Lundi à Vendredi
   - Heures : 7h00 à 17h00

![Capture d’écran 2025-03-07 100030](https://github.com/user-attachments/assets/943c683f-5dcf-40eb-af80-20fedd591ad1)

### Q.1.2.2 Restriction d'ordinateur
1. Propriétés du compte de Gabriel Ghul
2. Onglet "Compte" → "Se connecter à..."
3. Sélectionner "Suivants"
4. Ajouter "CLIENT01"

![Capture d’écran 2025-03-07 100731](https://github.com/user-attachments/assets/419d371e-2788-49d8-9b4e-e5712afc5799)


### Q.1.2.3 Stratégie de mot de passe
1. Ouvrir "Gestion des stratégies de groupe"
2. Créer une nouvelle GPO pour l'OU LabUsers
3. Dans l'éditeur de stratégie de groupe, naviguez jusqu'à :
     ```
     Configuration de l'ordinateur > Stratégies > Paramètres Windows > Paramètres de sécurité > Stratégies de compte > Stratégie de mot de passe
     ```

4. Configurer les paramètres de sécurité :
   - Longueur minimale : 12 caractères
   - Complexité requise
   - Durée de validité : 60 jours
   - Historique : 5 mots de passe

![Capture d’écran 2025-03-07 101601](https://github.com/user-attachments/assets/33e2d7cd-4cb7-43c9-9bcd-1c833a128c5f)

![Capture d’écran 2025-03-07 101757](https://github.com/user-attachments/assets/a1ea954d-1221-40ad-a175-9c87009ec881)

## 3. Lecteurs réseaux

### Q.1.3.1 Création de la GPO Drive-Mount

1. Les partages réseau pour les lecteurs E: et F: doivent être configurés et accessibles sur le réseau.
   - Exemple : 
     - E: correspond à `\\Serveur\PartageE`
     - F: correspond à `\\Serveur\PartageF`


2. Ouvrir "Gestion des stratégies de groupe"
3. Créer une nouvelle GPO nommée "Drive-Mount"
4. . Configurer le montage des lecteurs réseau
- Dans l'éditeur, naviguez jusqu'à :
  ```
  Configuration utilisateur > Préférences > Paramètres Windows > Lecteurs réseau
  ```
- Faites un clic droit dans le panneau de droite, puis sélectionnez **Nouveau > Lecteur réseau**.

##### Configuration pour le lecteur E:
. **Action** : Sélectionnez **Créer**.
. **Emplacement** : Entrez le chemin UNC du partage réseau pour le lecteur E:, par exemple : `\\Srvwin01\e`.
. **Lettre du lecteur** : Sélectionnez **E:** dans la liste déroulante.
. **Reconnecter** : Cochez cette option pour que le lecteur soit monté à chaque connexion.
. **Connexion** : Laissez l'option par défaut (**Se connecter en tant qu'utilisateur connecté**).
. Cliquez sur **OK** pour valider.

##### Configuration pour le lecteur F:
- Répétez les mêmes étapes que pour le lecteur E:, mais :
  - **Emplacement** : Entrez le chemin UNC du partage réseau pour le lecteur F:, par exemple : `\\Svrwin01\f`.
  - **Lettre du lecteur** : Sélectionnez **F:** dans la liste déroulante.

5. Lier la GPO au domaine ou à l'OU appropriée


![Capture d’écran 2025-03-07 102828](https://github.com/user-attachments/assets/d9903c30-d2a0-442b-82ad-4d7716dff3be)



