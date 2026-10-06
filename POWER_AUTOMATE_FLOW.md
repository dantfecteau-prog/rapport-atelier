# 📋 Configuration Power Automate - Rapport Atelier ARM

## Objectif
Recevoir les rapports du formulaire HTML et les enregistrer dans un Excel sur OneDrive.

---

## 📊 Structure Excel attendue

**Nom du fichier :** `Rapports-Atelier-ARM.xlsx`  
**Feuille :** `Rapports`

| Timestamp | Poste | Description | Impact | Employé | Email | Statut |
|-----------|-------|-------------|--------|---------|-------|--------|
| 2024-10-06T10:30:45Z | Banc de scie | La lame reste bloquée | Arrêt production | dfecteau | dfecteau@raynaldmarcoux.com | Reçu |

### Colonnes détaillées :
- **Timestamp** : Date/heure du rapport (format ISO)
- **Poste** : Lieu du problème (ex: CNC, Edgeuse, etc.)
- **Description** : Description complète du problème
- **Impact** : Sévérité (Arrêt production, Ralentissement, Qualité, Mineur)
- **Employé** : Nom de la personne qui rapporte
- **Email** : Email de la personne
- **Statut** : "Reçu" (remplissage auto dans Power Automate)

---

## 🔧 Configuration du Flow Power Automate

### Étape 1 : Créer le trigger

1. Va sur https://make.powerautomate.com
2. Clique sur **"+ Créer"** → **"Flux cloud"** → **"Flux instantané"**
3. Nomme-le : `Rapport Atelier - Recevoir et Enregistrer`
4. Sélectionne le trigger : **"Quand une requête HTTP est reçue"**

**Schéma JSON du body :**
```json
{
  "type": "object",
  "properties": {
    "timestamp": {
      "type": "string"
    },
    "poste": {
      "type": "string"
    },
    "description": {
      "type": "string"
    },
    "impact": {
      "type": "string"
    },
    "employe": {
      "type": "string"
    },
    "email": {
      "type": "string"
    },
    "photo": {
      "type": "string"
    }
  }
}
```

5. Clique sur **Créer** → tu verras l'**URL HTTP POST** générée
6. **Copie cette URL** et colle-la dans le formulaire HTML (⚙️ Paramètres)

---

### Étape 2 : Ajouter l'action "Créer un fichier"

> **Important :** Si tu n'as pas encore d'Excel, crée-le d'abord sur OneDrive

1. Clique sur **"+ Nouvelle étape"**
2. Cherche **"Excel Online (Business)"**
3. Sélectionne l'action : **"Ajouter une ligne à un tableau"**

#### Configuration :
- **Emplacement** : Sélectionne ton OneDrive personnel
- **Bibliothèque de documents** : `Documents` (ou le dossier ARM-Rapports-Atelier)
- **Fichier** : `Rapports-Atelier-ARM.xlsx`
- **Tableau** : `Rapports`

#### Remplissage des colonnes dynamiques :
```
Timestamp    → body('Ajouter_une_ligne_à_un_tableau')?['body/timestamp']
Poste        → body('Ajouter_une_ligne_à_un_tableau')?['body/poste']
Description  → body('Ajouter_une_ligne_à_un_tableau')?['body/description']
Impact       → body('Ajouter_une_ligne_à_un_tableau')?['body/impact']
Employé      → body('Ajouter_une_ligne_à_un_tableau')?['body/employe']
Email        → body('Ajouter_une_ligne_à_un_tableau')?['body/email']
Statut       → "Reçu"
```

**OU utilise le mode simple :**
- Clique sur le champ Timestamp
- Sélectionne `timestamp` dans la liste dynamique
- Répète pour chaque colonne

---

### Étape 3 : Ajouter une notification (optionnel)

1. Clique sur **"+ Nouvelle étape"**
2. Cherche **"Envoyer une notification Microsoft Teams"** (ou Email)
3. Configure :
   - **Canal** : `#ARM-Rapports`
   - **Message** : 
   ```
   ⚠️ NOUVEAU RAPPORT ATELIER
   
   Poste : @{body('Ajouter_une_ligne_à_un_tableau')?['body/poste']}
   Employé : @{body('Ajouter_une_ligne_à_un_tableau')?['body/employe']}
   Impact : @{body('Ajouter_une_ligne_à_un_tableau')?['body/impact']}
   
   Description : @{body('Ajouter_une_ligne_à_un_tableau')?['body/description']}
   ```

---

### Étape 4 : Sauvegarder et tester

1. Clique sur **Enregistrer**
2. Tu verras ton **URL HTTP POST** générée (copie-la)
3. Clique sur **Test** et utilise ce JSON pour tester :

```json
{
  "timestamp": "2024-10-06T10:30:45Z",
  "poste": "Banc de scie",
  "description": "La lame reste bloquée",
  "impact": "Arrêt production",
  "employe": "dfecteau",
  "email": "dfecteau@raynaldmarcoux.com",
  "photo": "non"
}
```

---

## 📱 Intégration dans le formulaire

### URL à copier dans le formulaire
- Va dans Power Automate
- Ouvre ton flow
- Clique sur le trigger "Quand une requête HTTP est reçue"
- Copie l'**URL HTTP POST complète**

### Colle dans le formulaire
1. Ouvre https://github.com/dantfecteau-prog/rapport-atelier/blob/main/rapport-atelier.html
2. Clique sur ⚙️ (Paramètres)
3. Colle l'URL dans le champ "URL Power Automate"
4. Email : `dfecteau@raynaldmarcoux.com` (déjà pré-rempli)
5. Clique sur ✓ Enregistrer

---

## ✅ Checklist finale

- [ ] Excel `Rapports-Atelier-ARM.xlsx` créé sur OneDrive
- [ ] Tableau nommé `Rapports` avec les bonnes colonnes
- [ ] Flow Power Automate créé et testé
- [ ] URL du webhook copiée dans le formulaire
- [ ] Premier test : soumettre un rapport depuis le téléphone
- [ ] Vérifier que la ligne apparaît dans Excel

---

## 🐛 Dépannage

| Problème | Solution |
|----------|----------|
| "❌ Erreur lors de la soumission" | Vérifie que l'URL Power Automate est correcte (commence par `https://`) |
| "⏳ Rapport Local" | L'envoi a échoué. Vérifie la connexion WiFi et que Power Automate reçoit les données |
| "Aucune ligne dans Excel" | Vérifie que le nom du tableau est bien `Rapports` et que les colonnes correspondent |
| "Erreur de colonne dans Power Automate" | Assure-toi que les noms des colonnes Excel sont EXACTEMENT comme dans le tableau ci-dessus |

---

## 📞 Support
Si tu as besoin d'aide, montre-moi :
- L'URL HTTP POST que tu as reçue
- Le message d'erreur exact
- Une capture de ton Excel

