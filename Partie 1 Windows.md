# Tutoriel - Manipulations pratiques sur VM Windows

## Sommaire
1. [Gestion des utilisateurs](#1-gestion-des-utilisateurs)
2. [Restriction utilisateurs](#2-restriction-utilisateurs)
3. [Lecteurs réseaux](#3-lecteurs-réseaux)

## 1. Gestion des utilisateurs

### Q.1.1.1 Création de l'utilisateur Lionel Lemarchand
1. Ouvrir "Utilisateurs et ordinateurs Active Directory"
2. Localiser l'OU où se trouve Kelly Rhameur
3. Clic droit → Nouveau → Utilisateur
4. Remplir les informations :
   - Prénom : Lionel
   - Nom : Lemarchand
   - Copier les attributs de société depuis le compte de Kelly Rhameur

### Q.1.1.2 Déplacement du compte Kelly Rhameur
1. Créer une nouvelle OU "DeactivatedUsers"
   - Clic droit sur le domaine → Nouveau → Unité d'organisation
   - Nommer "DeactivatedUsers"
2. Désactiver le compte de Kelly Rhameur
   - Clic droit sur son compte → Désactiver
3. Déplacer le compte dans l'OU "DeactivatedUsers"
   - Glisser-déposer ou couper-coller

### Q.1.1.3 Modification du groupe
1. Mettre à jour les membres du groupe
2. Retirer Kelly Rhameur
3. Ajouter Lionel Lemarchand

### Q.1.1.4 Gestion des dossiers
1. Créer le dossier pour Lionel Lemarchand
2. Renommer le dossier de Kelly Rhameur :
   ```
   ren "Kelly.Rhameur" "Kelly.Rhameur-ARCHIVE"
   ```

## 2. Restriction utilisateurs

### Q.1.2.1 Restriction horaire pour Gabriel Ghul
1. Dans "Utilisateurs et ordinateurs Active Directory"
2. Propriétés du compte de Gabriel Ghul
3. Onglet "Compte" → "Heures de connexion"
4. Configurer :
   - Jours : Lundi à Vendredi
   - Heures : 7h00 à 17h00

### Q.1.2.2 Restriction d'ordinateur
1. Propriétés du compte de Gabriel Ghul
2. Onglet "Compte" → "Se connecter à..."
3. Sélectionner "Suivants"
4. Ajouter "CLIENT01"

### Q.1.2.3 Stratégie de mot de passe
1. Ouvrir "Gestion des stratégies de groupe"
2. Créer une nouvelle GPO pour l'OU LabUsers
3. Configurer les paramètres de sécurité :
   - Longueur minimale : 12 caractères
   - Complexité requise
   - Durée de validité : 90 jours
   - Historique : 24 mots de passe

## 3. Lecteurs réseaux

### Q.1.3.1 Création de la GPO Drive-Mount
1. Ouvrir "Gestion des stratégies de groupe"
2. Créer une nouvelle GPO nommée "Drive-Mount"
3. Éditer la GPO :
   - Configuration utilisateur
   - Préférences → Mappages de lecteurs
4. Ajouter deux nouveaux lecteurs :
   - Lecteur E: → Chemin réseau du partage
   - Lecteur F: → Chemin réseau du partage
5. Lier la GPO au domaine ou à l'OU appropriée
